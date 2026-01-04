# BreezyBuilder

**Production software from vision documents. Big projects, tiny pieces, dumb execution loops.**

## What It Does

You give it a project overview. It:

1. **Deliberates** — 4 experts discuss WHAT to build until they have nothing new to add
2. **Decomposes** — Same experts discuss HOW to break it into atomic pieces
3. **Executes** — Builds each piece with implement → verify → review loops

You interact at key decision points. Everything else runs autonomously.

---

## Installation

```bash
/plugin marketplace add yourusername/breezynetworks
/plugin install breezybuilder@breezynetworks
```

Restart Claude Code after installing.

---

## Quick Start

### 1. Initialize

```bash
cd your-project
/breezybuilder:init
```

Creates `.breezybuilder/` with:
- `required-stack.md` — Tech stack (edit if needed)
- `potential-toolbox.md` — Available tools (edit if needed)

### 2. Plan

```bash
/breezybuilder:plan
```

1. Paste your project overview
2. Experts deliberate (minimum 5 rounds, until exhausted)
3. Answer any questions they surface
4. They decompose into phases and pieces
5. Select your demo strategy

### 3. Execute

```bash
/breezybuilder:execute
```

Builds piece by piece. Pauses at demo points based on your strategy.

---

## Commands

| Command | Purpose |
|---------|---------|
| `/breezybuilder:init` | Create `.breezybuilder/` with config files |
| `/breezybuilder:plan` | Run deliberation → decomposition → build-order |
| `/breezybuilder:execute` | Build piece by piece following build-order |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Continue from where you left off |

---

## How Planning Works

### Four Experts

| Expert | Focus |
|--------|-------|
| **Analyst** | Business logic gaps, ambiguities, edge cases |
| **Architect** | Tech decisions, dependencies, implementation |
| **Designer** | UX flows, component structure, accessibility |
| **Senior Dev** | Challenges all three, risks, what they missed |

### Exhaustion Rule

Experts deliberate in rounds. Each round, all four contribute. They continue until **all four** say "NOTHING NEW" in the same round.

Minimum 5 rounds before exhaustion is checked.

### User Input

After exhaustion, a synthesizer extracts:
- **Questions** — Things only you can answer
- **Decisions** — Things experts agreed on, for you to confirm

You respond, then 5 more rounds run to incorporate your input.

### Decomposition

Same pattern for breaking the project into:
- **Phases** — Logical groupings (Foundation, Schema, API, UI, etc.)
- **Pieces** — Atomic units with clear acceptance criteria
- **Demo Points** — Where you can see progress and give feedback

---

## How Execution Works

### For Each Piece

```
Code Select → Implement → Verify (2x) → Senior Review → Complete
```

1. **Code Select** — Identifies which files and decision sections to load (keeps context small)
2. **Implement** — Writes code to meet acceptance criteria using decision specs
3. **Verify** — Checks all criteria pass (needs 2 consecutive passes)
4. **Senior Review** — Final quality gate
5. **Complete** — Updates build-order.md, moves to next piece

### Demo Points

At decomposition, you choose:
1. **Full build** — Pause only at ship point
2. **Every demo point** — Pause after each milestone phase
3. **Specific phases** — Pause at phases you specify

At each demo point:
- **Continue** — Proceed to next phase
- **Feedback** — Note issues (minor fix or major revision)
- **Stop (MVP)** — Ship what's done

---

## File Structure

```
your-project/
├── .breezybuilder/
│   ├── required-stack.md          # Your tech stack
│   ├── potential-toolbox.md       # Available tools
│   ├── filtered-toolbox.md        # Tools for THIS project
│   ├── project-overview.md        # Your vision (captured)
│   ├── planning/
│   │   ├── planning-deliberation.md   # Expert discussions
│   │   ├── planning-decisions.md      # Structured decisions for execution
│   │   └── planning-decomposition.md  # Breakdown discussions
│   └── execution/
│       ├── build-order.md         # Phases + pieces + status
│       ├── demo-log.md            # Demo point records
│       └── revisions/             # If major changes needed
└── src/                           # Your actual code
```

**Commit `.breezybuilder/`** — It's your planning audit trail.

---

## Customization

### Change Tech Stack

Edit `.breezybuilder/required-stack.md` **before** running `/breezybuilder:plan`:

```markdown
## Framework
- Remix
- TypeScript

## Database  
- SQLite
- Prisma ORM

## Deployment
- Fly.io
```

### Add Custom Tools

Edit `.breezybuilder/potential-toolbox.md`:

```markdown
## Custom
- Internal API — https://docs.internal.company.com
- Legacy System — SOAP endpoint
```

---

## Default Stack

If you don't customize, BreezyBuilder defaults to:

- **Framework:** Next.js 15 (App Router), TypeScript
- **Styling:** Tailwind CSS 4, shadcn/ui
- **Database:** PostgreSQL, Drizzle ORM
- **Auth:** Clerk
- **Deployment:** Vercel

---

## The Philosophy

> Big projects don't need smart agents — they need small pieces and dumb loops.

| Principle | Why |
|-----------|-----|
| Fresh context per agent | Prevents drift |
| Exhaustion-based termination | Prevents shallow work |
| Atomic pieces | Prevents context overflow |
| Dumb loops | Prevents clever failures |

---

## Tips

1. **Write detailed overviews** — More detail = better deliberation
2. **Answer questions clearly** — Experts use your answers everywhere
3. **Use demo points** — Get feedback early, avoid surprises
4. **Trust exhaustion** — Let experts finish before moving on
5. **Commit .breezybuilder/** — It's your audit trail

---

## Troubleshooting

**"Run /breezybuilder:init first"**
→ Initialize before planning

**Deliberation exceeds 50 rounds**
→ Overview may be too complex; consider breaking into smaller projects

**Piece fails verification repeatedly**
→ Check Verify agent's issues; acceptance criteria may be unclear

**Need major changes mid-execution**
→ Use "Feedback → Major" at a demo point to trigger revision flow

---

## License

MIT
