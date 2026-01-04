---
description: Start BreezyBuilder planning phase. Runs multi-expert deliberation to determine WHAT to build, then decomposition to determine HOW to break it up. Requires .breezybuilder/ to exist (run /breezybuilder:init first).
argument-hint: <paste your project overview when prompted>
---

# BreezyBuilder Plan

Orchestrate the full planning phase: deliberation → decomposition → build-order.

## Pre-flight Checks

1. Verify `.breezybuilder/` exists with required-stack.md and potential-toolbox.md
   - If missing: "Run /breezybuilder:init first"
2. Check if planning already complete (build-order.md exists)
   - If exists: "Planning already complete. Run /breezybuilder:execute to build, or delete .breezybuilder/execution/build-order.md to re-plan"

## Phase 1: Capture Overview

1. Prompt user: "Paste your project overview. This can be prose, feature lists, user stories, or any description of what you want to build. Type END on a new line when done."
2. Capture all input until END
3. Write to `.breezybuilder/project-overview.md`:
   ```markdown
   # Project Overview
   
   Captured: [timestamp]
   Source: User input via /breezybuilder:plan
   
   ---
   
   [USER'S PASTED CONTENT]
   ```
4. Create empty `.breezybuilder/planning/planning-deliberation.md`:
   ```markdown
   # Planning Deliberation
   
   Started: [timestamp]
   Project: [first line of overview or "Untitled"]
   
   ---
   
   ```

## Phase 2: Filter Toolbox

Invoke the `breezybuilder-toolbox-filter` subagent:
- Pass: project-overview.md, potential-toolbox.md
- Receive: filtered-toolbox.md content
- Write to `.breezybuilder/filtered-toolbox.md`

## Phase 3: Deliberation Loop

Track: `round = 0`, `exhaustion_count = 0`

### Loop until exhausted:

```
round += 1

Append to planning-deliberation.md:
"## Round {round}\n\n"

1. Invoke `breezybuilder-analyst` subagent
   - Pass: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md
   - Append output to planning-deliberation.md

2. Invoke `breezybuilder-architect` subagent
   - Pass: same files (now includes Analyst output)
   - Append output to planning-deliberation.md

3. Invoke `breezybuilder-senior-dev` subagent
   - Pass: same files (now includes Analyst + Architect output)
   - Append output to planning-deliberation.md

4. Check exhaustion (only if round >= 5):
   - Read latest round from planning-deliberation.md
   - If all 3 experts said "NOTHING NEW": exhaustion_count += 1
   - Else: exhaustion_count = 0
   
5. If exhaustion_count >= 1 AND round >= 5:
   - Break loop (experts exhausted)
```

## Phase 4: Synthesize Decisions

**NEW: Run BEFORE deliberation synthesizer**

1. Invoke `breezybuilder-decisions-synthesizer` subagent
   - Pass: planning-deliberation.md, planning-decisions.md (if exists from prior cycle)
   - Receive: structured decisions content
   - Write to `.breezybuilder/planning/planning-decisions.md`

This extracts architecture decisions, integration specs, cost controls, error handling patterns, and business rules into a structured format for downstream agents.

## Phase 5: Synthesize & Get User Input

1. Invoke `breezybuilder-deliberation-synthesizer` subagent
   - Pass: planning-deliberation.md
   - Receive: questions, decisions, closed items

2. If questions or decisions exist:
   - Present to user in clear format
   - Wait for user responses
   - Append to planning-deliberation.md:
     ```markdown
     ---
     
     ## User Response [N]
     
     QUESTIONS:
     Q1: [user answer]
     Q2: [user answer]
     
     DECISIONS:
     D1: [confirmed/override: user's choice]
     
     ---
     ```
   - Run 5 more deliberation rounds (go back to Phase 3 loop)
   - After those rounds exhaust, run Phase 4 again (update planning-decisions.md)

3. If no questions and no decisions:
   - Deliberation complete, proceed to decomposition

## Phase 6: Decomposition

1. Create `.breezybuilder/planning/planning-decomposition.md`:
   ```markdown
   # Planning Decomposition
   
   Started: [timestamp]
   Deliberation Completed: [timestamp]
   Total Deliberation Rounds: [N]
   
   ---
   
   ```

2. Run decomposition loop (same pattern as deliberation):
   - Minimum 5 rounds
   - Same 3 experts with decomposition focus
   - Check exhaustion after round 5
   - Experts focus on: pieces, dependencies, sequence, sizing
   - Experts can reference planning-decisions.md for specific requirements

3. After exhaustion, invoke `breezybuilder-decomposition-synthesizer`:
   - Extract phases, pieces, demo points
   - Present to user

## Phase 7: Demo Strategy Selection

Present to user:
```
PHASES IDENTIFIED:

Phase 1: [name] — [N pieces]
Phase 2: [name] — [N pieces]
  ◆ DEMO POINT after this phase
Phase 3: [name] — [N pieces]
...
Phase N: [name] — [N pieces]
  ◆ SHIP POINT

---

SELECT DEMO STRATEGY:

1. Full build — pause only at ship point
2. Pause at every demo point for feedback  
3. Pause at specific demo points (enter phase numbers, comma-separated)

Enter 1, 2, or 3 (then phase numbers if 3):
```

Capture user selection.

## Phase 8: Write Build Order

1. Invoke `breezybuilder-build-order-writer` subagent
   - Pass: planning-deliberation.md, planning-decisions.md, planning-decomposition.md, user's demo strategy selection
   - Receive: complete build-order.md content

2. Write to `.breezybuilder/execution/build-order.md`

3. Create empty `.breezybuilder/execution/demo-log.md`:
   ```markdown
   # Demo Log
   
   Build Started: [timestamp]
   Demo Strategy: [from build-order.md]
   
   ---
   
   ```

## Completion

Output to user:
```
✓ Planning complete

Created:
- planning-deliberation.md — [N] rounds of expert deliberation
- planning-decisions.md — [N] decisions extracted
- planning-decomposition.md — [N] rounds of decomposition
- build-order.md — [N] phases, [N] pieces

Ready to build. Run /breezybuilder:execute to start execution.
```

## Error Handling

- If any subagent fails, log error and retry once
- If deliberation exceeds 50 rounds, warn user and ask to continue or force synthesis
- If decomposition exceeds 30 rounds, warn user and ask to continue or force synthesis

## State Recovery

If command is interrupted:
- Check for existing planning files
- Resume from last complete phase
- User can run /breezybuilder:status to see current state
