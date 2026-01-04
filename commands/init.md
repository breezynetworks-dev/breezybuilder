---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with defaults.md, potential-toolbox.md, and design-system.md from your global config (~/.breezybuilder/) or built-in defaults.
---

# BreezyBuilder Init

Initialize BreezyBuilder for this project.

## Steps

1. **Check if already initialized**
   - If `.breezybuilder/` exists with files, inform user:
     "BreezyBuilder already initialized. Run /breezybuilder:plan to start planning, or delete .breezybuilder/ to reset."
   - Exit if already initialized

2. **Create directory structure**
   ```
   .breezybuilder/
   ├── defaults.md            ← Your preferences by project type
   ├── potential-toolbox.md   ← Your curated tool catalog
   ├── design-system.md       ← Your UI patterns
   ├── planning/
   └── execution/
   ```

3. **Copy config files (with global config precedence)**
   
   For each file (defaults.md, potential-toolbox.md, design-system.md):
   ```
   if ~/.breezybuilder/{file} exists:
       Copy from ~/.breezybuilder/{file}
       Note: "Using global config"
   else:
       Copy from plugin's templates/ directory
       Note: "Using built-in default"
   ```

4. **Output to user**
   ```
   ✓ BreezyBuilder initialized
   
   Created:
   - .breezybuilder/defaults.md — [Using global config | Using built-in default]
   - .breezybuilder/potential-toolbox.md — [Using global config | Using built-in default]
   - .breezybuilder/design-system.md — [Using global config | Using built-in default]
   
   Global config location: ~/.breezybuilder/
   
   To set up global config (recommended):
   1. Create ~/.breezybuilder/ directory
   2. Copy and customize files you want to reuse across projects
   3. Future projects will use your global config automatically
   
   Next steps:
   1. Review the config files — Do they match your preferences?
   2. Run /breezybuilder:plan to start planning your project
   ```

## Global Config

Users can create `~/.breezybuilder/` with their preferred configs:

```
~/.breezybuilder/
├── defaults.md            ← Your preferences by project type
├── potential-toolbox.md   ← Your curated tool catalog
└── design-system.md       ← Your UI patterns
```

**Precedence:** Global config → Built-in templates

This allows users to:
- Set up once, reuse everywhere
- Maintain consistent preferences across projects
- Build a personal toolbox over time
- Define UI patterns that match their style

## Default Templates

If no global config exists, use built-in defaults from the plugin's `templates/` directory:
- `templates/defaults.md`
- `templates/potential-toolbox.md`
- `templates/design-system.md`

## Migration from required-stack.md

If upgrading from an older version with `required-stack.md`:
- The new `defaults.md` replaces it
- Stack choices are now made during intake based on project type
- Specific tool choices (auth, payments, etc.) come from toolbox
