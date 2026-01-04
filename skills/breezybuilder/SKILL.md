# BreezyBuilder Skill

Build production software from a vision document. Big project, tiny pieces, dumb execution loop.

## Commands

| Command | Description |
|---------|-------------|
| `/breezybuilder:init` | Initialize project with config files |
| `/breezybuilder:plan` | Start planning (intake → deliberation → decomposition) |
| `/breezybuilder:execute` | Start execution (requires build-order.md) |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Resume from last known state |

## How It Works

1. **Init** — Create config files from global config (~/.breezybuilder/) or built-in defaults
2. **Plan** — Multi-expert deliberation until exhausted, then decompose into atomic pieces
3. **Execute** — For each piece: implement → verify → senior review. Fresh context every iteration.

## Config Files

### Global Config (Optional)
```
~/.breezybuilder/
├── required-stack.md      ← Your universal tech preferences
├── potential-toolbox.md   ← Your curated tool catalog
└── design-system.md       ← Your UI patterns
```

### Project Config
```
.breezybuilder/
├── required-stack.md      ← From global or default
├── potential-toolbox.md   ← From global or default
├── design-system.md       ← From global or default
├── project-overview.md    ← Your vision (created during plan)
├── planning/
│   ├── planning-deliberation.md
│   ├── planning-decisions.md
│   └── planning-decomposition.md
└── execution/
    ├── build-order.md
    └── demo-log.md
```

## Core Principles

| Principle | Why |
|-----------|-----|
| Fresh context per iteration | Prevents drift |
| Exhaustion-based termination | Prevents shallow work |
| Atomic pieces | Prevents context overflow |
| Dumb loops | Prevents clever failures |
| Design system | Consistent UI without decisions per piece |

## Four Expert Roles

| Role | Focus |
|------|-------|
| Analyst | Business logic gaps, edge cases |
| Architect | Implementation, tech decisions |
| Designer | UX flows, accessibility, DS-XXX proposals |
| Senior Dev | Challenges all three, finds risks |

## Piece Types

| Type | Description | Design Context |
|------|-------------|----------------|
| backend | API routes, DB, jobs | Skip |
| frontend | UI components, pages | Load |
| fullstack | API + UI combined | Load |

## Decision Types

| Prefix | Category |
|--------|----------|
| AD-XXX | Architecture Decisions |
| IS-XXX | Integration Specifications |
| DM-XXX | Data Model Additions |
| CC-XXX | Cost Controls |
| EH-XXX | Error Handling Patterns |
| BR-XXX | Business Rules |
| DS-XXX | Design Standards (refinements) |
| DP-XXX | Deferred to Production |

## Execution Loop

```
For each piece:
  Code Selector → identifies files + design context
  Implement → builds the piece
  Verify → checks criteria (2x required)
  Senior Review → final approval
  Mark complete → next piece
```

## Demo Points

User chooses strategy:
1. Full build — pause only at ship point
2. Pause at every demo point
3. Pause at specific demo points

At demo points:
- Continue
- Feedback (minor fix or major revision)
- Stop here (MVP)

## Context Management

- Target 80k tokens per agent (of 200k available)
- Design context only for frontend/fullstack pieces
- Code selection filters to relevant files only
- Decisions filtered to relevant sections only

## Quick Start

```
/breezybuilder:init          # Set up config files
# Review/edit config files
/breezybuilder:plan          # Paste your overview, answer questions
# Experts deliberate until exhausted
# Choose demo strategy
/breezybuilder:execute       # Build begins
```
