# BreezyNetworks

Claude Code plugins for building production software faster.

## Installation

```bash
/plugin marketplace add yourusername/breezynetworks
```

Then install any plugin:

```bash
/plugin install <plugin-name>@breezynetworks
```

---

## Plugins

### 🏗️ BreezyBuilder

**Production software from vision documents.**

Turn a project overview into working code through multi-expert deliberation, atomic decomposition, and dumb execution loops.

```bash
/plugin install breezybuilder@breezynetworks
```

#### Philosophy

> Big projects don't need smart agents — they need small pieces and dumb loops.

- **Fresh context per agent** — prevents drift
- **Exhaustion-based termination** — prevents shallow work  
- **Atomic pieces** — prevents context overflow
- **Dumb loops** — prevents clever failures

#### Quick Start

```bash
# 1. Initialize in your project
/breezybuilder:init

# 2. Plan (paste your vision, experts deliberate)
/breezybuilder:plan

# 3. Execute (builds piece by piece)
/breezybuilder:execute
```

#### How It Works

**Planning Phase:**
1. Three experts (Analyst, Architect, Senior Dev) deliberate until exhausted
2. You answer questions they can't resolve
3. They decompose into phases and atomic pieces
4. You select demo points for feedback

**Execution Phase:**
1. For each piece: Implement → Verify (2x) → Senior Review
2. Pause at demo points for your feedback
3. Ship when ready

#### Commands

| Command | Purpose |
|---------|---------|
| `/breezybuilder:init` | Create config files |
| `/breezybuilder:plan` | Run planning phase |
| `/breezybuilder:execute` | Build piece by piece |
| `/breezybuilder:status` | Show current state |
| `/breezybuilder:resume` | Continue from last state |

[Full documentation →](./breezybuilder/README.md)

---

## Coming Soon

More plugins in development. Watch this repo for updates.

---

## License

MIT
