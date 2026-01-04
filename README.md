# BreezyBuilder

**Production software from vision documents. Big projects, tiny pieces, dumb execution loops.**

## What It Does

You give it a project overview. It:

1. **Intake** — Asks targeted questions to fill gaps (infrastructure, auth, payments, etc.)
2. **Deliberates** — 4 experts discuss WHAT to build until they have nothing new to add
3. **Decomposes** — Same experts discuss HOW to break it into atomic pieces
4. **Executes** — Builds each piece with implement → verify → review loops

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
- `required-stack.md` — Your universal preferences (framework, styling)
- `potential-toolbox.md` — Your curated tool catalog (grow over time)

**These are YOUR preferences**, not project-specific. Edit once, reuse across projects.

### 2. Plan

```bash
/breezybuilder:plan
```

1. Paste your project overview
2. **Intake phase** — Answer quick questions (local/remote, auth, payments, etc.)
3. Experts deliberate (minimum 5 rounds, until exhausted)
4. Answer any questions they surface
5. They decompose into phases and pieces
6. Select your demo strategy

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
│   ├── required-stack.md          # Your universal preferences
│   ├── potential-toolbox.md       # Your curated tool catalog
│   ├── filtered-toolbox.md        # Tools selected for THIS project
│   ├── project-overview-raw.md    # Your original vision (immutable)
│   ├── project-overview.md        # Enriched with intake answers
│   ├── planning/
│   │   ├── planning-deliberation.md   # Expert discussions
│   │   ├── planning-decisions.md      # Structured decisions for execution
│   │   └── planning-decomposition.md  # Breakdown discussions
│   └── execution/
│       ├── build-order.md         # Phases + pieces + status
│       ├── demo-log.md            # Demo point records
│       └── revisions/             # If major changes needed
├── docker-compose.yml             # Local services (if LOCAL infrastructure)
├── .env.example                   # Env template (if REMOTE infrastructure)
└── src/                           # Your actual code
```

**Commit `.breezybuilder/`** — It's your planning audit trail.

---

## Customization

### Your Universal Preferences

Edit `.breezybuilder/required-stack.md` to set your go-to technologies:

```markdown
## Framework
- Remix
- TypeScript

## Styling
- Tailwind CSS
- Chakra UI

## Deployment
- Fly.io
```

These apply to ALL your projects. Edit once, reuse everywhere.

### Grow Your Tool Catalog

Add tools to `.breezybuilder/potential-toolbox.md` as you discover good options:

```markdown
## Authentication

### Supertokens
- **Use for:** Self-hosted auth with more control than Clerk
- **Docs:** https://supertokens.com/docs

## Custom/Internal

### Company Internal API
- **Use for:** Legacy system integration
- **Docs:** https://docs.internal.company.com
```

The intake phase will show relevant options from your catalog.

---

## Default Stack

If you don't customize, BreezyBuilder defaults to:

- **Framework:** Next.js 15 (App Router), TypeScript
- **Styling:** Tailwind CSS 4, BaseUI, shadcn/ui
- **Deployment:** Vercel

**Project-specific selections** (auth, database, payments) are made during the intake phase based on your toolbox.

---

## Infrastructure

During intake, you choose:

**LOCAL (Recommended for development)**
- Docker Compose for Postgres, Redis
- Still needs API keys for services without local alternatives (Clerk, Stripe, Firecrawl)
- Best for development — database/cache work offline

**REMOTE (For cloud services)**
- All services use cloud infrastructure
- All API keys required upfront

**Both modes:**
- Create `.env.example` with all required variables
- Block until you configure `.env`
- **Verify all credentials work** before proceeding to deliberation

This verification catches broken API keys early — not during piece 47 of execution.

You can migrate from LOCAL to REMOTE later when deploying.

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
