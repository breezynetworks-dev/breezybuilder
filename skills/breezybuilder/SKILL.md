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

1. **Intake** — Ask targeted questions to fill gaps
   - Infrastructure: LOCAL (Docker) or REMOTE (cloud services)
   - Auth, payments, theme, platform based on overview gaps
   - Creates enriched project-overview.md

2. **Deliberation** — 4 experts discuss WHAT to build until exhausted
   - Analyst: Business logic, gaps, edge cases
   - Architect: Tech decisions, implementation concerns
   - Designer: UX flows, component structure, accessibility
   - Senior Dev: Challenges all three, finds risks
   - Minimum 5 rounds, continue until all say "NOTHING NEW"

3. **Decisions Synthesis** — Extract structured decisions from deliberation
   - Architecture decisions, integration specs, cost controls
   - Error handling patterns, business rules
   - Creates planning-decisions.md for downstream agents

4. **User Synthesis** — Extract questions for user, decisions to confirm

5. **Decomposition** — Same experts discuss HOW to break it up
   - Phases, pieces, dependencies, demo points
   - Same exhaustion pattern

6. **Build Order** — Write execution plan with acceptance criteria
   - References decision IDs for specific requirements

### Execution Phase

For each piece:
1. **Code Select** — Identify relevant files AND decision sections (keep context small)
2. **Implement** — Write code to meet acceptance criteria using decision specs
3. **Verify** — Check criteria (need 2x VERIFIED)
4. **Senior Review** — Final approval
5. **Mark Complete** — Update build-order.md

Demo points pause for user feedback. User can continue, provide feedback, or stop (MVP).

## File Structure

```
.breezybuilder/
├── required-stack.md          # Universal preferences (all projects)
├── potential-toolbox.md       # Curated tool catalog
├── filtered-toolbox.md        # Tools for THIS project
├── project-overview-raw.md    # Original user input (immutable)
├── project-overview.md        # Enriched with intake answers
├── planning/
│   ├── planning-deliberation.md
│   ├── planning-decisions.md  # Structured decisions for execution
│   └── planning-decomposition.md
└── execution/
    ├── build-order.md
    ├── demo-log.md
    └── revisions/             # If major changes
```

## Context Management

- Target 80k tokens per agent (160k real limit)
- Structured expert output (~60% token reduction)
- Code selection loads only relevant files + decision sections
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

## Decisions Format

planning-decisions.md structures all resolved decisions:

```markdown
## Architecture Decisions
### AD-001: [name]
**Decision:** [what]
**Implementation:** [specific details]

## Cost Controls
| ID | Control | Limit | Enforcement |
|CC-001| [what] | [limit] | [how] |

## Error Handling Patterns
### EH-001: [scenario]
**Trigger:** [cause]
**Response:** [action]
```

Implement agents receive relevant decision sections to know exactly what to build.

## Exhaustion Rule

All 4 experts must say "NOTHING NEW" in the same round. If even one has something new, another round runs. Minimum 5 rounds before checking exhaustion.

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
3. Create revision-XXX-decisions.md (scoped to change)
4. Update build-order.md
5. Resume execution
