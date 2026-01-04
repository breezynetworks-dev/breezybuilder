---
description: Start BreezyBuilder execution phase. Builds the project piece by piece following build-order.md. Requires planning to be complete (run /breezybuilder:plan first).
---

# BreezyBuilder Execute

Orchestrate the execution phase: implement → verify → review each piece.

## Pre-flight Checks

1. Verify `.breezybuilder/execution/build-order.md` exists
   - If missing: "Run /breezybuilder:plan first to create build order"
2. Verify `.breezybuilder/planning/planning-decisions.md` exists
   - If missing: Warn but continue (older planning without decisions synthesis)
3. Parse build-order.md to find current state
   - Find first piece with `[ ]` (not complete)
   - If all pieces `[x]`: "Build complete! Project shipped. 🚀"

## Execution Loop

For each pending piece in build-order.md:

### Step 1: Select Code Context

1. Invoke `breezybuilder-code-selector` subagent
   - Pass: current piece section from build-order.md, project file structure, planning-decisions.md
   - Receive: list of files to load, list of decision sections to load, estimated tokens

2. If estimated tokens > 50k:
   - Warn: "Piece may be too large. Consider splitting."
   - Ask user to continue or pause

3. Load identified files AND decision sections into context for next steps

### Step 2: Implement

1. Invoke `breezybuilder-implement` subagent
   - Pass: required-stack.md, current piece (name, dependencies, acceptance criteria), relevant code files, relevant decision sections from planning-decisions.md
   - Subagent writes code files directly to project

2. Track: `verify_count = 0`

### Step 3: Verify Loop

```
Loop until 2x VERIFIED or max 10 attempts:

1. Invoke `breezybuilder-verify` subagent
   - Pass: current piece + acceptance criteria, relevant code files
   - Receive: VERIFIED or ISSUES

2. If VERIFIED:
   verify_count += 1
   If verify_count >= 2: break (proceed to Senior Review)
   Else: continue loop (verify again)

3. If ISSUES:
   verify_count = 0
   Invoke `breezybuilder-implement` subagent again with issues + decision sections
   Continue loop

4. If attempts >= 10:
   Warn user: "Piece failed verification 10 times"
   Ask: Continue trying, skip piece, or stop execution
```

### Step 4: Senior Review

1. Invoke `breezybuilder-senior-review` subagent
   - Pass: current piece + acceptance criteria, relevant code files
   - Receive: APPROVED or ISSUES

2. If ISSUES:
   - Reset verify_count = 0
   - Return to Step 2 (Implement) with issues
   - Loop continues

3. If APPROVED:
   - Proceed to mark complete

### Step 5: Mark Complete

1. Update build-order.md:
   - Change `- [ ] Piece X.Y` to `- [x] Piece X.Y`
   - Update phase status if all pieces in phase complete

2. Check for demo point after this piece/phase

### Step 6: Demo Point Check

If demo point reached AND user selected to pause at this demo point:

1. Append to demo-log.md:
   ```markdown
   ## Demo Point #[N]
   After: Phase [N] ([name])
   Date: [timestamp]
   
   ### What's working:
   - [list completed functionality]
   
   ### User choice: [pending]
   ```

2. Present to user:
   ```
   ◆ DEMO POINT REACHED
   
   Phase [N]: [name] complete
   
   What's working:
   - [functionality list]
   
   OPTIONS:
   1. Continue — proceed to next phase
   2. Feedback — note issues before continuing
   3. Stop here (MVP) — execution ends
   
   Enter 1, 2, or 3:
   ```

3. Handle user choice:
   - **1 (Continue)**: Update demo-log.md, proceed to next piece
   - **2 (Feedback)**: Capture feedback, ask "Minor or Major change?"
     - Minor: Fix in current context, continue
     - Major: Trigger revision flow (see below)
   - **3 (Stop)**: Update demo-log.md with "STOPPED", end execution

### Step 7: Next Piece

- Move to next `[ ]` piece in build-order.md
- Return to Step 1

## Revision Flow (Major Feedback)

When user indicates "Major" change at demo point:

1. Create revision files:
   ```
   .breezybuilder/execution/revisions/
   ├── revision-001-deliberation.md
   ├── revision-001-decisions.md
   ├── revision-001-decomposition.md
   └── revision-001-build-order.md
   ```

2. Initialize revision-001-deliberation.md:
   ```markdown
   # Revision 001 — Deliberation
   
   Triggered: [timestamp]
   After: Demo Point #[N] (Phase [N]: [name])
   Reason: [brief summary]
   
   ## Current State
   
   Phases complete:
   - [list completed phases]
   
   Code exists for:
   - [summary of what's built]
   
   ## User Feedback
   
   [user's feedback verbatim]
   
   ---
   
   ```

3. Run revision deliberation loop:
   - Same 4 experts, minimum 5 rounds
   - Focus: What needs to change? What's unaffected? New pieces needed?

4. Run decisions synthesizer on revision deliberation:
   - Creates revision-001-decisions.md (scoped to revision)

5. Run revision decomposition:
   - Extract: MODIFIED pieces, ADDED pieces, REMOVED pieces

6. Write revision-001-build-order.md with changes

7. Merge changes into main build-order.md:
   - Insert new pieces at correct positions
   - Mark modified pieces for re-implementation
   - Add reference to revision-001 for audit trail

8. Merge new decisions into main planning-decisions.md:
   - Add new decisions with "Added: Revision 001" note
   - Update existing decisions if modified

9. Resume execution at first changed piece

## Ship Point

When final piece completes:

1. Update build-order.md: all phases COMPLETE
2. Append to demo-log.md:
   ```markdown
   ## Ship Point
   Date: [timestamp]
   
   ### Final State:
   - All [N] phases complete
   - All [N] pieces implemented and verified
   
   ### Action: SHIPPED 🚀
   ```

3. Output to user:
   ```
   ════════════════════════════════════════
   ✓ BUILD COMPLETE — SHIPPED 🚀
   ════════════════════════════════════════
   
   Phases: [N] complete
   Pieces: [N] implemented
   Revisions: [N]
   
   Your project is ready for deployment.
   
   Audit trail:
   - .breezybuilder/planning/planning-deliberation.md
   - .breezybuilder/planning/planning-decisions.md
   - .breezybuilder/planning/planning-decomposition.md
   - .breezybuilder/execution/build-order.md
   - .breezybuilder/execution/demo-log.md
   ```

## Error Handling

- If subagent fails, retry once before warning user
- If piece fails 10 verify attempts, pause for user decision
- If code selector finds no relevant files, warn and ask to continue
- If planning-decisions.md missing, warn but continue without decision sections
- Track all errors in demo-log.md for debugging
