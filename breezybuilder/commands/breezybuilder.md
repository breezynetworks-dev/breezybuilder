---
description: Run BreezyBuilder. Auto-detects state and continues from where you left off — planning, mockups, or execution.
argument-hint: <paste your project overview when prompted>
---

# BreezyBuilder

Unified command that auto-detects state and runs the appropriate phase.

## State Detection

Check files in `.breezybuilder/` to determine current state:

```
1. No .breezybuilder/ → Not initialized
2. Only preferences.md → Ready for planning
3. overview.md exists, no deliberation.md → Start deliberation
4. deliberation.md exists, incomplete → Resume deliberation
5. deliberation.md complete, no spec.md → Write spec
6. spec.md exists, no pieces.md → Mockup (if UI) then write pieces
7. pieces.md exists, has [ ] items → Execute/resume execution
8. pieces.md exists, all [x] → Complete
```

## Phase: Not Initialized

If `.breezybuilder/` doesn't exist:

```
BreezyBuilder not initialized.

Run /breezybuilder:init first.
```

Stop here.

## Phase: Capture Overview

If only `preferences.md` exists (no overview.md):

1. Prompt user:
   ```
   Paste your project overview. This can be prose, feature lists,
   user stories, or any description of what you want to build.

   Type END on a new line when done.
   ```
2. Capture all input until END
3. Write to `.breezybuilder/overview.md`:
   ```markdown
   # Overview

   Captured: [timestamp]

   ---

   [user's input verbatim]
   ```
4. Continue to Deliberation phase

## Phase: Detect Project Type

Read overview.md and determine project type:

| If overview mentions... | Type | Designer? |
|------------------------|------|-----------|
| "dashboard", "SaaS", "web app", "frontend" | Web App | Yes |
| "Flask", "FastAPI", "Django", "Python" | Python | Maybe |
| "CLI", "command line", "terminal" | CLI | No |
| "API only", "backend service" | Backend | No |

Store in state for use throughout.

## Phase: Deliberation

### Initialize (if no deliberation.md)

Create `.breezybuilder/deliberation.md`:
```markdown
# Deliberation

Started: [timestamp]
Project Type: [detected type]

---

```

### Resume (if deliberation.md exists)

Parse last round number and continue from there.

```
Resuming deliberation from Round [N]...
```

### Deliberation Loop

Track: `round = 0` (or resume from last)

```
round += 1

Append to deliberation.md:
"## Round {round}\n\n"

1. Invoke `breezybuilder-developer` subagent
   - Pass: ## Developer from preferences.md, overview.md, deliberation.md
   - Append output to deliberation.md

2. If project has UI, invoke `breezybuilder-designer` subagent
   - Pass: ## Designer from preferences.md, overview.md, deliberation.md
   - Append output to deliberation.md

3. Invoke `breezybuilder-product` subagent
   - Pass: ## Product from preferences.md, overview.md, deliberation.md
   - Append output to deliberation.md

4. Collect questions from all experts this round

5. If questions exist:
   - Present to user
   - Capture answers
   - Append to deliberation.md:
     ```markdown
     ### User Answers (Round {round})

     Q: [question]
     A: [answer]
     ```

6. Check exhaustion (only if round >= 5):
   - Read latest round from deliberation.md
   - If all experts said "NOTHING NEW": break loop
```

### Exhaustion Rules

- Minimum 5 rounds before checking exhaustion
- All participating experts must say "NOTHING NEW" in same round
- If round > 15, warn user and ask to continue or proceed

## Phase: Write Spec

1. Invoke `breezybuilder-write-spec` subagent
   - Pass: overview.md, deliberation.md
   - Agent writes to `.breezybuilder/spec.md`

2. Present spec summary to user:
   ```
   ═══════════════════════════════════════════════════════════════
   SPEC COMPLETE
   ═══════════════════════════════════════════════════════════════

   What We're Building:
   [summary from spec.md]

   Key Decisions:
   - [decision 1]
   - [decision 2]

   Scope:
   - IN: [features in]
   - OUT: [features out]

   Review .breezybuilder/spec.md for full details.

   Press ENTER to continue, or type EDIT to modify spec.
   ```

3. If EDIT: Open spec.md for manual editing, wait for user to continue

## Phase: Mockup (Optional)

Only for UI projects (detected in Project Type phase).

### Step 1: Offer Mockup

After spec review:
```
Do you want to create a visual mockup before implementation?

This will:
- Create a branch with UI mockups using demo data
- Let you preview designs before building
- Mockups become the starting point (demo data removed during implementation)

[Yes] / [No]
```

If No: Skip to Write Pieces phase.

### Step 2: Git Setup

1. Stash uncommitted changes:
   ```bash
   git stash push -m "breezy-mockup-stash"
   ```

2. Create mockup branch:
   ```bash
   git checkout -b mockup-v1
   ```

### Step 3: Generate Mockup

Invoke `breezybuilder-designer` subagent in mockup mode:
- Pass: `MODE: mockup`
- Pass: spec.md, preferences.md (## Designer section)
- Pass: existing code patterns (via Glob/Grep) if project has UI
- Tools: Read, Write, Edit, Glob, Grep, Skill
- Agent invokes `frontend-design` skill for visual generation

### Step 4: User Preview

```
═══════════════════════════════════════════════════════════════
MOCKUP READY
═══════════════════════════════════════════════════════════════

Branch: mockup-v1
Files created:
- [list of files]

Run your dev server to preview.

[Looks good] / [Try again] / [Cancel]

Optional feedback for "Try again": ___
```

### Step 5: Handle Response

**Looks good:**
```bash
git add -A && git commit -m "Mockup: UI design"
git checkout main
git merge mockup-v1 --no-edit
git branch -d mockup-v1
git stash pop || true
```

Update spec.md `## Mockup` section:
```markdown
## Mockup

Status: Complete
Files:
- [list of mockup files]

Note: Build on mockups. Remove demo data during implementation.
```

Continue to Write Pieces.

**Try again:**
```bash
git checkout -- .  # Discard changes
```
Re-invoke Designer with user feedback. Loop (max 5 attempts).

**Cancel:**
```bash
git checkout main
git branch -D mockup-v1
git stash pop || true
```
Continue to Write Pieces without mockup.

## Phase: Write Pieces

1. Invoke `breezybuilder-write-pieces` subagent
   - Pass: spec.md
   - Agent writes to `.breezybuilder/pieces.md`

2. Present pieces summary to user:
   ```
   ═══════════════════════════════════════════════════════════════
   BUILD ORDER COMPLETE
   ═══════════════════════════════════════════════════════════════

   Phases: [N]
   Pieces: [N] total

   Phase 1: [name] — [N pieces]
   Phase 2: [name] — [N pieces]
     ◆ DEMO POINT
   Phase 3: [name] — [N pieces]
   ...

   Review .breezybuilder/pieces.md for full details.
   ```

3. Output planning complete:
   ```
   ✓ Planning complete

   Files created:
   - overview.md — your original vision
   - deliberation.md — [N] rounds of expert discussion
   - spec.md — resolved specification
   - pieces.md — [N] phases, [N] pieces

   Starting execution...
   ```

4. Continue to Execution phase

## Phase: Execution

### Pre-flight

1. Verify `.breezybuilder/pieces.md` exists
2. Parse pieces.md to find current state:
   - Find first piece with `[ ]` (not complete)
   - If all pieces `[x]`: Show completion, stop

### Resume Check

If resuming (pieces.md has some `[x]` items):
```
Resuming execution...

Progress: [X]% complete
- Completed: [N] pieces
- Remaining: [M] pieces

Current piece: [Piece X.Y: name]

Continuing...
```

### Execution Loop

For each pending piece in pieces.md:

#### Step 1: Select Context

1. Invoke `breezybuilder-select-context` subagent
   - Pass: current piece section from pieces.md, spec.md, codebase access
   - Receive:
     - List of relevant code files (may be empty for greenfield)
     - Relevant spec sections
     - Relevant preferences sections
     - Mockup files (if exist)
     - Estimated token budget

2. If no relevant files found:
   - This is normal for early pieces (greenfield)
   - Continue with empty file list

3. If estimated tokens > 50k:
   - Warn: "Piece may be too large. Consider splitting."
   - Ask user to continue or pause

#### Step 2: Implement ↔ Verify Loop

1. Invoke `breezybuilder-implement` subagent (stays open)
   - Pass:
     - Current piece (name, type, acceptance criteria)
     - Relevant code files (from Select Context)
     - Relevant spec sections
     - Relevant preferences sections
     - Mockup files (if exist)

2. Invoke `breezybuilder-verify` subagent (stays open)
   - Pass:
     - Current piece (name, type, acceptance criteria)
     - Relevant spec sections
     - Relevant preferences sections (## Designer if frontend/fullstack)
     - Access to read codebase

3. Agents converse until Verify responds "VERIFIED"

4. Both agents despawn after VERIFIED

#### Step 3: Mark Complete

1. Update pieces.md:
   - Change `- [ ] Piece X.Y` to `- [x] Piece X.Y`

2. Check for demo point after this piece/phase

#### Step 4: Demo Point Check

If demo point reached:

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

Handle user choice:
- **1 (Continue)**: Proceed to next piece
- **2 (Feedback)**: Capture feedback, ask if minor or major
- **3 (Stop)**: End execution

#### Step 5: Next Piece

- Move to next `[ ]` piece in pieces.md
- Return to Step 1

## Phase: Complete

When all pieces are `[x]`:

```
════════════════════════════════════════
✓ BUILD COMPLETE
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

- If any subagent fails, log error and retry once
- If deliberation exceeds 20 rounds without exhaustion, warn and ask to continue
- If piece fails verification after many iterations, pause for user decision
- If user aborts, save current state for resume
- Track all errors for debugging

## State Recovery

If command is interrupted:
- State is preserved in `.breezybuilder/` files
- Run `/breezybuilder` again to resume from last point
