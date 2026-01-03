---
name: breezybuilder
description: Production software from vision documents using multi-expert deliberation, atomic decomposition, and dumb execution loops. Use this skill when the user wants to build a complete software project from a vision/overview document, or when working with BreezyBuilder planning and execution files.
---

# BreezyBuilder

Build production software from vision documents with minimal user involvement.

## Core Philosophy

**Big projects don't need smart agents — they need small pieces and dumb loops.**

- Fresh context per agent (prevents drift)
- Exhaustion-based termination (prevents shallow work)
- Atomic pieces (prevents context overflow)
- Dumb loops (prevents clever failures)

## Commands

| Command | Purpose |
|---------|---------|
| `/breezybuilder:init` | Create .breezybuilder/ with config files |
| `/breezybuilder:plan` | Run deliberation → decomposition → build-order |
| `/breezybuilder:execute` | Build piece by piece following build-order |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Continue from last known state |

## How It Works

### Planning Phase

1. **Deliberation** — 3 experts discuss WHAT to build until exhausted
   - Analyst: Business logic, gaps, edge cases
   - Architect: Tech decisions, implementation concerns
   - Senior Dev: Challenges both, finds risks
   - Minimum 5 rounds, continue until all say "NOTHING NEW"

2. **Synthesis** — Extract questions for user, decisions to confirm

3. **Decomposition** — Same experts discuss HOW to break it up
   - Phases, pieces, dependencies, demo points
   - Same exhaustion pattern

4. **Build Order** — Write execution plan with acceptance criteria

### Execution Phase

For each piece:
1. **Code Select** — Identify relevant files (keep context small)
2. **Implement** — Write code to meet acceptance criteria
3. **Verify** — Check criteria (need 2x VERIFIED)
4. **Senior Review** — Final approval
5. **Mark Complete** — Update build-order.md

Demo points pause for user feedback. User can continue, provide feedback, or stop (MVP).

## File Structure

```
.breezybuilder/
├── required-stack.md          # Non-negotiable tech stack
├── potential-toolbox.md       # Available tools
├── filtered-toolbox.md        # Tools for THIS project
├── project-overview.md        # User's vision
├── planning/
│   ├── planning-deliberation.md
│   └── planning-decomposition.md
└── execution/
    ├── build-order.md
    ├── demo-log.md
    └── revisions/             # If major changes
```

## Context Management

- Target 80k tokens per agent (160k real limit)
- Structured expert output (~60% token reduction)
- Code selection loads only relevant files
- No summarization (lossy)
- Files are state (no in-memory accumulation)

## Expert Output Format

Experts use structured format, not prose:

```markdown
### Analyst

GAPS:
- [gap]: [explanation]

EDGE CASES:
- [edge case]

QUESTIONS FOR USER:
- [question]

NOTHING NEW:
- [only when exhausted]
```

## Exhaustion Rule

All 3 experts must say "NOTHING NEW" in the same round. If even one has something new, another round runs. Minimum 5 rounds before checking exhaustion.

## Demo Points

User selects at decomposition:
1. Full build — pause only at ship
2. Pause at every demo point
3. Pause at specific demo points

At each pause, user chooses: Continue / Feedback / Stop (MVP)

## Revision Flow

Major feedback triggers:
1. Create revision-XXX-deliberation.md
2. Run focused deliberation on change
3. Update build-order.md
4. Resume execution
