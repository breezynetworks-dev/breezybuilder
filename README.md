# BreezyBuilder

**Production software from vision documents. Big projects, tiny pieces, dumb execution loops.**

## What It Does

You give it a project overview. It:

1. **Detects** — Identifies your project type (Web App, Python, CLI, etc.)
2. **Intake** — Asks targeted questions to fill gaps (infrastructure, auth, payments, component library, etc.)
3. **Deliberates** — 4 experts discuss WHAT to build until they have nothing new to add
4. **Decomposes** — Same experts discuss HOW to break it into atomic pieces
5. **Executes** — Builds each piece with implement → verify → review loops

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
- `defaults.md` — Smart defaults by project type
- `potential-toolbox.md` — Your curated tool catalog (including component libraries)
- `design-system.md` — Your design patterns (typography, spacing, components)

**First run:** Uses built-in defaults.
**After global setup:** Uses your personal defaults from `~/.breezybuilder/`.

### 2. Plan

```bash
/breezybuilder:plan
```

1. Paste your project overview
2. **Project type detected** — Web App, Python, CLI, etc.
3. **Intake phase** — Answer quick questions (infrastructure, component library, auth, etc.)
4. Experts deliberate (minimum 5 rounds, until exhausted)
5. Answer any questions they surface
6. They decompose into phases and pieces
7. Select your demo strategy

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
| `/breezybuilder:plan` | Run intake → deliberation → decomposition → build-order |
| `/breezybuilder:execute` | Build piece by piece following build-order |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Continue from where you left off |

---

## Global Config (One-Time Setup)

Save your preferences once, reuse across all projects:

```bash
# After customizing your first project
mkdir -p ~/.breezybuilder
cp .breezybuilder/defaults.md ~/.breezybuilder/
cp .breezybuilder/potential-toolbox.md ~/.breezybuilder/
cp .breezybuilder/design-system.md ~/.breezybuilder/
```

Now every `/breezybuilder:init` uses YOUR defaults, not built-in templates.

**Precedence:** Global config (`~/.breezybuilder/`) → Built-in templates

---

## How Planning Works

### Project Type Detection

Overview Parser detects project type from signals:

| Signals | Project Type |
|---------|--------------|
| "dashboard", "SaaS", "web app", "Next.js" | Web App |
| "FastAPI", "Django", "Flask", "Python" | Python |
| "CLI", "command line", "terminal" | CLI Tool |
| "API only", "microservice" | Backend Service |

Defaults are loaded for that project type. If unclear, you're asked.

### Four Experts

| Expert | Focus |
|--------|-------|
| **Analyst** | Business logic gaps, ambiguities, edge cases |
| **Architect** | Tech decisions, dependencies, implementation |
| **Designer** | UX flows, component structure, accessibility, **design refinements** |
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
- **Pieces** — Atomic units with clear acceptance criteria and types
- **Demo Points** — Where you can see progress and give feedback

---

## How Execution Works

### For Each Piece

```
Code Select → Implement → Verify (2x) → Senior Review → Complete
```

1. **Code Select** — Identifies files, decision sections, and design context (for UI pieces)
2. **Implement** — Writes code following acceptance criteria and design patterns
3. **Verify** — Checks all criteria including design compliance (needs 2 consecutive passes)
4. **Senior Review** — Final quality gate
5. **Complete** — Updates build-order.md, moves to next piece

### Piece Types

Each piece has a type that determines what context it receives:

| Type | Design Context? | Examples |
|------|-----------------|----------|
| `backend` | No | API routes, database, integrations |
| `frontend` | Yes | Pages, components, layouts |
| `fullstack` | Yes | Combined API + UI |

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

## Design System

`design-system.md` provides sensible defaults for:

- **Typography** — Scale from page titles to captions
- **Spacing** — Consistent padding and gap values
- **Layout Patterns** — Page, card grid, data table, stack
- **Component Patterns** — Forms, buttons, dialogs
- **State Patterns** — Loading, empty, error, success
- **Accessibility** — Required standards (contrast, keyboard, ARIA)

### Design Standards (DS-XXX)

When baseline patterns don't fit your project, Designer proposes refinements:

```markdown
### Designer

DS-XXX PROPOSALS:
- DS-001: Compact card variant
  - Baseline: p-4 padding (design-system.md default)
  - Proposed: p-3 padding, text-xs metadata
  - Rationale: User tracks 50+ competitors, needs higher density
```

These become DS-XXX decisions in `planning-decisions.md` and are enforced during execution.

---

## File Structure

```
~/.breezybuilder/                    # Global defaults (optional, one-time setup)
├── defaults.md
├── potential-toolbox.md
└── design-system.md

your-project/
├── .breezybuilder/
│   ├── defaults.md                  # Smart defaults by project type
│   ├── potential-toolbox.md         # Your tool catalog (including component libraries)
│   ├── design-system.md             # Your design patterns
│   ├── filtered-toolbox.md          # Tools selected for THIS project
│   ├── project-overview.md          # Enriched with intake answers + tech choices
│   ├── planning/
│   │   ├── planning-deliberation.md     # Expert discussions
│   │   ├── planning-decisions.md        # Structured decisions (AD-, IS-, DS-, etc.)
│   │   └── planning-decomposition.md    # Breakdown discussions
│   └── execution/
│       ├── build-order.md           # Phases + pieces + types + status
│       ├── demo-log.md              # Demo point records
│       └── revisions/               # If major changes needed
├── docker-compose.yml               # Local services (if LOCAL infrastructure)
├── .env.example                     # Env template
└── src/                             # Your actual code
```

**Commit `.breezybuilder/`** — It's your planning audit trail.

---

## Customization

### Your Default Preferences

Edit `.breezybuilder/defaults.md`:

```markdown
## Web App (default)
Framework: Remix
Language: TypeScript
Styling: Tailwind CSS 4
Deployment: Fly.io

## Python
Framework: Django
Package Manager: Poetry
```

### Your Tool Catalog

Add tools to `.breezybuilder/potential-toolbox.md`:

```markdown
## Component Libraries

### shadcn/ui + BaseUI
- **Use for:** Headless BaseUI primitives with shadcn styling
- **Docs:** https://ui.shadcn.com, https://base-ui.com

## Authentication

### Supertokens
- **Use for:** Self-hosted auth with more control than Clerk
- **Docs:** https://supertokens.com/docs
```

### Your Design Patterns

Edit `.breezybuilder/design-system.md`:

```markdown
## Typography
| Element | Classes |
|---------|---------|
| Page title | `text-3xl font-bold` |  # Your preference

## Component Patterns > Forms
- Submit button: Left-aligned  # Your preference
```

**After customizing, save to global config to reuse everywhere.**

---

## Default Stack (Web App)

If you don't customize, BreezyBuilder defaults to:

- **Framework:** Next.js 15 (App Router), TypeScript
- **Styling:** Tailwind CSS 4
- **Deployment:** Vercel

**Project-specific selections** (component library, auth, database, payments) are made during intake based on what your project needs.

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
2. **Set up global config** — One-time setup, consistent starting point
3. **Customize design-system.md** — Your patterns, not generic defaults
4. **Use demo points** — Get feedback early, avoid surprises
5. **Trust exhaustion** — Let experts finish before moving on
6. **Commit .breezybuilder/** — It's your audit trail

---

## Troubleshooting

**"Run /breezybuilder:init first"**
→ Initialize before planning

**Deliberation exceeds 50 rounds**
→ Overview may be too complex; consider breaking into smaller projects

**Piece fails verification repeatedly**
→ Check Verify agent's issues; acceptance criteria may be unclear

**UI doesn't match design expectations**
→ Check if DS-XXX refinements are applied; verify design-system.md patterns

**Need major changes mid-execution**
→ Use "Feedback → Major" at a demo point to trigger revision flow

---

## License

MIT
