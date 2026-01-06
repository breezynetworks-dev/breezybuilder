---
description: Start BreezyBuilder execution phase. Builds the project piece by piece following pieces.md. Requires planning to be complete (run /breezybuilder:plan first).
---

# BreezyBuilder Execute

Orchestrate execution: select context → implement ↔ verify → next piece.

## Pre-flight Checks

1. Verify `.breezybuilder/pieces.md` exists
   - If missing: "Run /breezybuilder:plan first"
2. Verify `.breezybuilder/spec.md` exists
   - If missing: Fatal error — re-run planning
3. Verify `.breezybuilder/preferences.md` exists
   - If missing: Warn but continue
4. Parse pieces.md to find current state
   - Find first piece with `[ ]` (not complete)
   - If all pieces `[x]`: "Build complete! 🚀"

## Execution Loop

For each pending piece in pieces.md:

### Step 1: Select Context

1. Invoke `breezybuilder-select-context` subagent
   - Pass: current piece section from pieces.md, spec.md, codebase access
   - Receive:
     - List of relevant code files (may be empty for greenfield)
     - Relevant spec sections
     - Relevant preferences sections
     - Estimated token budget

2. If no relevant files found:
   - This is normal for early pieces (greenfield)
   - Continue with empty file list

3. If estimated tokens > 50k:
   - Warn: "Piece may be too large. Consider splitting."
   - Ask user to continue or pause

### Step 2: Implement ↔ Verify Loop

1. Invoke `breezybuilder-implement` subagent (stays open)
   - Pass:
     - Current piece (name, type, acceptance criteria)
     - Relevant code files (from Select Context)
     - Relevant spec sections
     - Relevant preferences sections

2. Invoke `breezybuilder-verify` subagent (stays open)
   - Pass:
     - Current piece (name, type, acceptance criteria)
     - Relevant spec sections
     - Relevant preferences sections (## Designer if frontend/fullstack)
     - Access to read codebase

3. Agents converse:
   ```
   Implement: [writes code]
   Implement: Ready for verification

   Verify: [checks criteria]
   Verify: ISSUES:
   - [issue 1]
   - [issue 2]

   Implement: [fixes issues]
   Implement: Ready for verification

   Verify: VERIFIED
   ```

4. Loop until Verify responds "VERIFIED"

5. Both agents despawn after VERIFIED

### Step 3: Mark Complete

1. Update pieces.md:
   - Change `- [ ] Piece X.Y` to `- [x] Piece X.Y`

2. Check for demo point after this piece/phase

### Step 4: Demo Point Check

If demo point reached:

1. Present to user:
   ```
   ◆ DEMO POINT REACHED

   Phase [N]: [name] complete

   What's working:
   - [functionality list]

   OPTIONS:
   1. Continue — proceed to next phase
   2. Feedback — note issues before continuing
   3. Stop here — execution ends

   Enter 1, 2, or 3:
   ```

2. Handle user choice:
   - **1 (Continue)**: Proceed to next piece
   - **2 (Feedback)**: Capture feedback, ask if minor or major
     - Minor: Note and continue
     - Major: Pause for manual intervention
   - **3 (Stop)**: End execution

### Step 5: Next Piece

- Move to next `[ ]` piece in pieces.md
- Return to Step 1

## Ship Point

When final piece completes:

1. Update pieces.md: all pieces complete

2. Output to user:
   ```
   ════════════════════════════════════════
   ✓ BUILD COMPLETE 🚀
   ════════════════════════════════════════

   Phases: [N] complete
   Pieces: [N] implemented

   Files:
   - .breezybuilder/overview.md — original vision
   - .breezybuilder/deliberation.md — expert discussion
   - .breezybuilder/spec.md — resolved spec
   - .breezybuilder/pieces.md — build order

   Your project is ready.
   ```

## Error Handling

- If subagent fails, retry once before warning user
- If piece fails verification after many iterations, pause for user decision:
  ```
  Piece [X.Y] is having trouble passing verification.

  OPTIONS:
  1. Keep trying
  2. Skip this piece (mark incomplete)
  3. Stop execution

  Enter 1, 2, or 3:
  ```
- If Select Context finds no relevant files, continue (greenfield is fine)
- If spec.md missing, fatal error — cannot execute without spec
- Track all errors for debugging

## State Recovery

If command is interrupted:
- Check pieces.md for current state
- Resume from last incomplete piece
- User can run /breezybuilder:status to see current state
