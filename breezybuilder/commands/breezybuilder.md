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
8. pieces.md exists, all [x] → Run post-build validation
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
   - Pass: overview.md, deliberation.md, preferences.md
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

### Step 2: Invoke Mockup Coordinator

Invoke `breezybuilder-mockup-coordinator` subagent:
- Pass: spec.md, preferences.md
- Agent handles entire mockup loop internally:
  - Git setup (branch, stash)
  - Mockup generation (uses frontend-design skill)
  - User preview and feedback
  - Iterations (max 5 attempts)
  - Git cleanup (commit/merge or discard)
- Agent returns: `MOCKUP_STATUS: complete` or `MOCKUP_STATUS: cancelled`

**Why a dedicated agent:** The mockup feedback loop can involve multiple iterations. Running this in a subagent keeps all iterations contained, preventing context growth in the main conversation.

### Step 3: Handle Coordinator Result

**If `MOCKUP_STATUS: complete`:**
- Note the files created
- Continue to Write Pieces

**If `MOCKUP_STATUS: cancelled`:**
- Continue to Write Pieces without mockup

**If `MOCKUP_STATUS: handoff`:**
- Coordinator hit 20 iterations and needs fresh context
- Spawn a NEW `breezybuilder-mockup-coordinator` subagent
- The new coordinator will read `.breezybuilder/mockup-handoff.md` and continue
- Loop back to handle the new coordinator's result

## Phase: Write Pieces

1. Invoke `breezybuilder-write-pieces` subagent
   - Pass: spec.md, preferences.md
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
2. Parse pieces.md to identify all phases
3. If all pieces `[x]`: Skip to Post-Build Validation

### Resume Check

If resuming (pieces.md has some `[x]` items):
```
Resuming execution...

Progress: [X]% complete
- Phases complete: [N]/[M]
- Pieces complete: [N]/[M]

Continuing from Phase [N]...
```

### Phase-by-Phase Execution

Execute each phase using a dedicated coordinator subagent.

**Why:** Each phase runs in isolated context, preventing main conversation from accumulating piece-by-piece details. If a phase has many pieces, the coordinator hands off internally.

#### For Each Phase:

1. **Identify current phase:**
   - Find first phase with any `[ ]` pieces
   - Get phase number and name

2. **Invoke `breezybuilder-execution-coordinator` subagent:**
   - Pass: phase number, pieces.md, spec.md, preferences.md
   - Coordinator handles all pieces in that phase:
     - Select context per piece
     - Implement ↔ verify loop per piece
     - Updates pieces.md as pieces complete
     - Hands off internally if 10+ pieces

3. **Handle coordinator result:**

   **If `PHASE_COMPLETE`:**
   ```
   ═══════════════════════════════════════════════════════════════
   PHASE [N] COMPLETE: [Name]
   ═══════════════════════════════════════════════════════════════

   Pieces completed: [N]
   - [Piece X.1]: [summary]
   - [Piece X.2]: [summary]
   - ...

   Progress: [N]/[M] phases, [N]/[M] pieces

   Continuing to Phase [N+1]...
   ```
   Continue to next phase.

   **If `HANDOFF_NEEDED`:**
   - Coordinator hit 10 pieces and needs fresh context
   - Spawn NEW `breezybuilder-execution-coordinator` for same phase
   - It reads `execution-state.md` and continues
   - Loop until phase complete

   **If `PIECE_BLOCKED`:**
   - A piece failed verification repeatedly
   - Show user the issue
   - Ask: [Skip piece] / [Pause execution]
   - If skip: mark piece as blocked, coordinator continues
   - If pause: stop execution, user can resume later

4. **Phase complete → next phase:**
   - Delete `execution-state.md` if exists
   - Continue to next phase with `[ ]` pieces

### All Phases Complete

When all phases done, continue to Post-Build Validation.

## Phase: Post-Build Validation

When all pieces are `[x]`, run validation before declaring complete.

### Step 1: Environment Check

1. **Find required env vars:**
   - First, check for `.env.example` — use as source of truth
   - If no `.env.example`, grep codebase for `process.env.` and `import.meta.env.`
   - Build list of required variables

2. **Check `.env` file exists:**
   - If missing: "Create a `.env` file in project root"
   - Wait for user to confirm

3. **Check each required var:**
   - Is it present in `.env`?
   - Is it set (not empty)?
   - Is it not a placeholder? (detect patterns like `your-api-key-here`, `xxx`, `changeme`, `<your-key>`)

4. **Report status:**
   ```
   ═══════════════════════════════════════════════════════════════
   ENVIRONMENT CHECK
   ═══════════════════════════════════════════════════════════════

   Required variables:
   ✓ DATABASE_URL — set
   ✓ OPENAI_API_KEY — set
   ✗ STRIPE_SECRET_KEY — missing
   ✗ AUTH_SECRET — placeholder detected ("changeme")

   Please update your .env file and press Enter to re-check.
   ```

5. **Loop until all vars pass**, then continue.

### Step 2: Connectivity Check

1. **Identify services from spec.md `## Technical Stack`:**
   - Database (Postgres, MySQL, etc.)
   - Auth provider (if external)
   - External APIs (Stripe, OpenAI, etc.)

2. **For each service, run appropriate check:**

   | Service Type | Check Method |
   |--------------|--------------|
   | PostgreSQL | `SELECT 1` query via connection string |
   | MySQL | `SELECT 1` query via connection string |
   | Redis | `PING` command |
   | External API | Health endpoint or simple authenticated request |
   | Auth provider | Token validation endpoint |

3. **Report status:**
   ```
   ═══════════════════════════════════════════════════════════════
   CONNECTIVITY CHECK
   ═══════════════════════════════════════════════════════════════

   Services:
   ✓ PostgreSQL — connected (23ms)
   ✓ OpenAI API — responding
   ✗ Stripe API — failed: Invalid API key

   Please fix the issues and press Enter to re-check.
   ```

4. **Loop until all services pass**, then continue.

### Step 3: Validation Complete

```
═══════════════════════════════════════════════════════════════
✓ BUILD & VALIDATION COMPLETE
═══════════════════════════════════════════════════════════════

Phases: [N] complete
Pieces: [N] implemented
Environment: ✓ configured
Services: ✓ connected

Your project is ready for testing.

[Test automatically] / [Test manually]
```

**If "Test automatically":**
- Run `/breezybuilder:test` (to be implemented)
- Comprehensive feature testing

**If "Test manually":**
```
Ready for manual testing.

When you want automated testing, run:
  /breezybuilder:test

Files:
- .breezybuilder/overview.md — original vision
- .breezybuilder/spec.md — resolved spec
- .breezybuilder/pieces.md — build order

To add more features, just run /breezybuilder and describe what you want.
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
