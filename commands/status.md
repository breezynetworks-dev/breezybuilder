---
description: Show current BreezyBuilder state. Displays planning progress, execution progress, and next steps.
---

# BreezyBuilder Status

Show the current state of BreezyBuilder for this project.

## Check State

1. **Not initialized**
   - If `.breezybuilder/` doesn't exist:
     ```
     BreezyBuilder: Not initialized

     Run /breezybuilder:init to get started.
     ```

2. **Initialized, not planned**
   - If `.breezybuilder/` exists but no deliberation.md:
     ```
     BreezyBuilder: Initialized

     ✓ preferences.md

     Next: Run /breezybuilder:plan to start planning.
     ```

3. **Planning in progress**
   - If deliberation.md exists but no pieces.md:
     ```
     BreezyBuilder: Planning in progress

     Project Type: [Web App / Python / CLI / Backend]

     Deliberation: Round [N]
     - Developer: [done/pending]
     - Designer: [done/pending/skipped]
     - Product: [done/pending]

     Spec: [not written / written]

     Next: Run /breezybuilder:plan to continue.
     ```

4. **Planning complete, not executing**
   - If pieces.md exists but execution not started:
     ```
     BreezyBuilder: Ready to execute

     Planning:
     - Deliberation: [N] rounds
     - Spec: written
     - Pieces: [N] total

     Build Order:
     - Phases: [N]
     - Pieces: [N] (backend: [n], frontend: [n], fullstack: [n])
     - Demo points: [N]

     Next: Run /breezybuilder:execute to start building.
     ```

5. **Execution in progress**
   - Parse pieces.md for progress:
     ```
     BreezyBuilder: Executing

     Progress:
     - Phase [X] of [N]: [name]
     - Piece [Y] of [M]: [name]
     - Overall: [%] complete

     Current piece: [name]
     - Type: [backend / frontend / fullstack]
     - Status: [implementing/verifying]

     Completed:
     - Pieces: [n] / [total]

     Next: Run /breezybuilder:execute to continue.
     ```

6. **Execution complete**
   - If all pieces marked `[x]`:
     ```
     BreezyBuilder: Complete 🚀

     Summary:
     - Phases: [N] ✓
     - Pieces: [N] ✓

     Files:
     - overview.md — original vision
     - deliberation.md — expert discussion
     - spec.md — resolved spec
     - pieces.md — build order

     Project ready.
     ```

## Parse Logic

- Count `[x]` vs `[ ]` in pieces.md for progress
- Parse piece Types from pieces.md for type breakdown
- Check phase headers for phase progress
- Count rounds in deliberation.md
