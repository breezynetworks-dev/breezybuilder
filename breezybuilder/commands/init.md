---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with preferences.md.
---

# BreezyBuilder Init

Initialize BreezyBuilder for this project.

## Steps

1. **Check for global config**
   - Read `~/.breezybuilder/preferences.md`
   - If it exists, copy it to `.breezybuilder/preferences.md` and note "Using global config"
   - If it doesn't exist, continue to step 2

2. **Create .breezybuilder/preferences.md with default template**
   - Create the `.breezybuilder/` directory (if it doesn't exist)
   - Write the **Default Template** (below) to `.breezybuilder/preferences.md` (overwrite if exists)
   - Note "Using built-in default"

3. **Output to user**
   ```
   ✓ BreezyBuilder initialized

   Created:
   - .breezybuilder/preferences.md — [Using global config | Using built-in default]

   Next steps:
   1. Review preferences.md — Does it match your style?
   2. Run /breezybuilder:plan to start planning your project
   ```

## Global Config

Users can create `~/.breezybuilder/preferences.md` with their preferred config. If it exists, init uses it instead of the default template.

## Default Template

Copy this exactly to `.breezybuilder/preferences.md`:

```markdown
# Preferences

Your preferences for how projects should be built.

> **Note:** Do not rename or create custom headings. The following sections are expected by the planning agents:
> - `## Developer` — Technical preferences (stack, database, tools)
> - `## Designer` — Visual/UX preferences (components, patterns)
> - `## Product` — Product preferences (scope, MVP, user flows)

---

## Developer

- **Database:** PostgreSQL + Drizzle — Local Docker postgres:16, deploy remote later
- **Time-series:** TimescaleDB + Drizzle — Local Docker timescale/timescaledb, deploy remote later

---

## Designer

- **Stack:** BaseUI + shadcn/ui + Tailwind 4 (all components already installed)
- **Loading:** Skeleton for content, spinner for buttons
- **Empty:** Centered message with optional CTA
- **Error:** Inline for forms, toast for actions

---

## Product

- Ship MVP first, iterate later
```
