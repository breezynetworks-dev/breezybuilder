---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with preferences.md from your global config (~/.breezybuilder/) or built-in defaults.
---

# BreezyBuilder Init

Initialize BreezyBuilder for this project.

## Steps

1. **Check if already initialized**
   - If `.breezybuilder/` exists with preferences.md:
     "BreezyBuilder already initialized. Run /breezybuilder:plan to start planning, or delete .breezybuilder/ to reset."
   - Exit if already initialized

2. **Create directory structure**
   ```
   .breezybuilder/
   └── preferences.md    ← Your preferences (Developer, Designer, Product)
   ```

3. **Copy preferences.md (with global config precedence)**

   ```
   if ~/.breezybuilder/preferences.md exists:
       Copy from ~/.breezybuilder/preferences.md
       Note: "Using global config"
   else:
       Copy from plugin's templates/preferences.md
       Note: "Using built-in default"
   ```

4. **Output to user**
   ```
   ✓ BreezyBuilder initialized

   Created:
   - .breezybuilder/preferences.md — [Using global config | Using built-in default]

   Global config location: ~/.breezybuilder/

   To set up global config (recommended):
   1. Create ~/.breezybuilder/ directory
   2. Copy and customize preferences.md
   3. Future projects will use your global config automatically

   Next steps:
   1. Review preferences.md — Does it match your style?
   2. Run /breezybuilder:plan to start planning your project
   ```

## Global Config

Users can create `~/.breezybuilder/` with their preferred config:

```
~/.breezybuilder/
└── preferences.md    ← Your preferences
```

**Precedence:** Global config → Built-in template

This allows users to:
- Set up once, reuse everywhere
- Maintain consistent preferences across projects
- Customize their stack, tools, and design patterns

## preferences.md Structure

The preferences file has three sections, one for each expert:

```markdown
## Developer
- Philosophy (simple over clever, etc.)
- Stack defaults (framework, language, etc.)
- Tools I like (database, auth, payments, etc.)

## Designer
- Philosophy (minimal, content-first, etc.)
- Typography patterns
- Spacing patterns
- Layout patterns
- Component patterns
- State patterns (loading, empty, error)
- Accessibility requirements

## Product
- Philosophy (ship fast, iterate, etc.)
- Project type detection hints
- Questions that matter to you
- Scope preferences
```

## Default Template

If no global config exists, use built-in default from the plugin's `templates/preferences.md`.
