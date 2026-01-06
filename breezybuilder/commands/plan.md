---
description: Start BreezyBuilder planning phase. Runs multi-expert deliberation to determine WHAT to build and HOW to break it up, then creates spec and pieces. Requires .breezybuilder/ to exist (run /breezybuilder:init first).
argument-hint: <paste your project overview when prompted>
---

# BreezyBuilder Plan

Orchestrate planning: deliberation → spec → pieces.

## Pre-flight Checks

1. Verify `.breezybuilder/` exists with preferences.md
   - If missing: "Run /breezybuilder:init first"
2. Check if planning already complete (pieces.md exists)
   - If exists: "Planning complete. Run /breezybuilder:execute to build, or delete .breezybuilder/pieces.md to re-plan"

## Phase 1: Capture Overview

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

## Phase 2: Detect Project Type

Read overview.md and determine project type:

| If overview mentions... | Type | Designer? |
|------------------------|------|-----------|
| "dashboard", "SaaS", "web app", "frontend" | Web App | Yes |
| "Flask", "FastAPI", "Django", "Python" | Python | Maybe |
| "CLI", "command line", "terminal" | CLI | No |
| "API only", "backend service" | Backend | No |

Store in state for use throughout planning.

## Phase 3: Initialize Deliberation

Create `.breezybuilder/deliberation.md`:
```markdown
# Deliberation

Started: [timestamp]
Project Type: [detected type]

---

```

## Phase 4: Deliberation Loop

Track: `round = 0`

### Loop until exhausted:

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

## Phase 5: Write Spec

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

   Press ENTER to continue to pieces, or type EDIT to modify spec.
   ```

3. If EDIT: Open spec.md for manual editing, wait for user to continue

## Phase 6: Write Pieces

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

## Completion

Output to user:
```
✓ Planning complete

Files created:
- overview.md — your original vision
- deliberation.md — [N] rounds of expert discussion
- spec.md — resolved specification
- pieces.md — [N] phases, [N] pieces

Ready to build. Run /breezybuilder:execute to start.
```

## Error Handling

- If any subagent fails, log error and retry once
- If deliberation exceeds 20 rounds without exhaustion, warn and ask to continue
- If user aborts, save current state for resume

## State Recovery

If command is interrupted:
- Check for existing files (overview.md, deliberation.md, spec.md, pieces.md)
- Resume from last complete phase
- User can run /breezybuilder:status to see current state
