# BreezyBuilder

**Production software from vision documents. Big projects, tiny pieces, dumb execution loops.**

## What It Does

You give it a project overview. It:

1. **Deliberates** — 3 experts discuss WHAT to build and HOW to break it up
2. **Resolves** — Creates a spec from the discussion + your answers
3. **Decomposes** — Breaks into atomic pieces with acceptance criteria
4. **Executes** — Builds each piece with implement ↔ verify loops

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
- `preferences.md` — Your preferences (Developer, Designer, Product sections)

**First run:** Uses built-in defaults.
**After global setup:** Uses your personal preferences from `~/.breezybuilder/`.

### 2. Plan

```bash
/breezybuilder:plan
```

1. Paste your project overview
2. Experts deliberate (WHAT + HOW, asking questions along the way)
3. Answer their questions
4. Spec is written (resolved requirements + decisions)
5. Pieces are created (build order with acceptance criteria)

### 3. Execute

```bash
/breezybuilder:execute
```

Builds piece by piece. Pauses at demo points for feedback.

---

## Commands

| Command | Purpose |
|---------|---------|
| `/breezybuilder:init` | Create `.breezybuilder/` with preferences |
| `/breezybuilder:plan` | Run deliberation → spec → pieces |
| `/breezybuilder:execute` | Build piece by piece |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Continue from where you left off |

---

## Global Config (One-Time Setup)

Save your preferences once, reuse across all projects:

```bash
# After customizing your first project
mkdir -p ~/.breezybuilder
cp .breezybuilder/preferences.md ~/.breezybuilder/
```

Now every `/breezybuilder:init` uses YOUR preferences.

**Precedence:** Global config (`~/.breezybuilder/`) → Built-in templates

---

## How Planning Works

### Three Experts

| Expert | Focus |
|--------|-------|
| **Developer** | Tech decisions, architecture, feasibility, stack |
| **Designer** | UX flows, component structure, accessibility, patterns |
| **Product** | Requirements, scope, contradictions, edge cases |

Designer is skipped for backend-only, CLI, or API projects.

### Deliberation Flow

Experts deliberate in rounds. Each round:
1. Each expert contributes questions + discussion
2. You answer any questions
3. Next round incorporates your answers

They continue until **all experts** say "NOTHING NEW" in the same round.

### Spec + Pieces

After deliberation:
1. **Write Spec** — Resolves overview + Q&A into clean requirements
2. **Write Pieces** — Breaks spec into atomic units with acceptance criteria

---

## How Execution Works

### For Each Piece

```
Select Context → Implement ↔ Verify → Next Piece
```

1. **Select Context** — Identifies relevant code and decisions for this piece
2. **Implement** — Writes code following acceptance criteria
3. **Verify** — Checks all criteria, provides feedback
4. **Loop** — Implement and Verify converse until VERIFIED
5. **Next** — Move to next piece

### Piece Types

Each piece has a type that determines what context it receives:

| Type | Design Context? | Examples |
|------|-----------------|----------|
| `backend` | No | API routes, database, integrations |
| `frontend` | Yes | Pages, components, layouts |
| `fullstack` | Yes | Combined API + UI |

### Demo Points

At demo points:
- **Continue** — Proceed to next phase
- **Feedback** — Note issues before continuing
- **Stop** — Ship what's done

---

## File Structure

```
~/.breezybuilder/                    # Global preferences (optional)
└── preferences.md

your-project/
├── .breezybuilder/
│   ├── preferences.md               # Your preferences
│   ├── overview.md                  # Original vision (never modified)
│   ├── deliberation.md              # Expert discussions + Q&A
│   ├── spec.md                      # Resolved specification
│   └── pieces.md                    # Build order with acceptance criteria
└── src/                             # Your actual code
```

**Commit `.breezybuilder/`** — It's your planning audit trail.

---

## Preferences

`preferences.md` has three sections:

### ## Developer
- Philosophy (simple over clever, etc.)
- Stack defaults (framework, language, styling)
- Tools I like (database, auth, payments, etc.)

### ## Designer
- Philosophy (minimal, content-first, etc.)
- Typography patterns
- Spacing patterns
- Layout patterns
- Component patterns
- State patterns (loading, empty, error)
- Accessibility requirements

### ## Product
- Philosophy (ship fast, iterate, etc.)
- Project type detection hints
- Questions that matter
- Scope preferences

---

## Customization

Edit `.breezybuilder/preferences.md`:

```markdown
## Developer

### Philosophy
- Simple over clever
- Minimal dependencies

### Stack Defaults

**Web App:**
- Framework: Remix  # Your preference
- Deployment: Fly.io

### Tools I Like

#### Database
- **Supabase** — Postgres + realtime + auth bundle
```

**After customizing, save to global config to reuse everywhere.**

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
3. **Customize preferences** — Your patterns, not generic defaults
4. **Use demo points** — Get feedback early, avoid surprises
5. **Trust exhaustion** — Let experts finish before moving on
6. **Commit .breezybuilder/** — It's your audit trail

---

## Troubleshooting

**"Run /breezybuilder:init first"**
→ Initialize before planning

**Deliberation runs too long**
→ Overview may be too complex; consider breaking into smaller projects

**Piece fails verification repeatedly**
→ Check issues; acceptance criteria may be unclear

---

## Summary

| | Before | After |
|--|--------|-------|
| Files | 10+ | 5 |
| Agents | 15 | 8 |
| Config files | 3 | 1 |
| Deliberation phases | 2 | 1 |

Simpler. Cleaner. Same results.

---

## License

MIT
