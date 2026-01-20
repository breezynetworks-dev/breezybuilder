---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with preferences.md.
---

# BreezyBuilder Init

Initialize BreezyBuilder for this project.

## Steps

1. **Check git repository**
   - Check if `.git/` directory exists in the current project
   - If it doesn't exist, run `git init` to initialize a new repository
   - Note "Initialized git repository" if created, otherwise skip silently

2. **Check for frontend-design plugin**
   - Run `claude plugin list` and capture output
   - Check if `frontend-design@claude-plugins-official` appears in the output
   - If NOT found, set a warning flag to display later
   - Do NOT block initialization — just warn

3. **Check for global config**
   - Read `~/.breezybuilder/preferences.md`
   - If it exists, copy it to `.breezybuilder/preferences.md` and note "Using global config"
   - If it doesn't exist, continue to step 4

4. **Create .breezybuilder/preferences.md with default template**
   - Create the `.breezybuilder/` directory (if it doesn't exist)
   - Write the **Default Template** (below) to `.breezybuilder/preferences.md` (overwrite if exists)
   - Note "Using built-in default"

5. **Output to user**
   ```
   ✓ BreezyBuilder initialized

   Created:
   - .breezybuilder/preferences.md — [Using global config | Using built-in default]
   [- Initialized git repository (if created)]

   Next steps:
   1. Review preferences.md — Does it match your style?
   2. Run /breezybuilder to start
   ```

   **If frontend-design plugin was NOT found, append:**
   ```
   ⚠ Recommended plugin not installed

   BreezyBuilder uses the frontend-design plugin for UI mockups.
   Install it with:

     claude plugin install frontend-design@claude-plugins-official

   You can still use BreezyBuilder without it, but mockups may be lower quality.
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
