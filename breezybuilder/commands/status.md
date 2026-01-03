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
   - If `.breezybuilder/` exists but no planning-deliberation.md:
     ```
     BreezyBuilder: Initialized
     
     ✓ required-stack.md
     ✓ potential-toolbox.md
     
     Next: Run /breezybuilder:plan to start planning.
     ```

3. **Planning in progress**
   - If planning-deliberation.md exists but no build-order.md:
     ```
     BreezyBuilder: Planning in progress
     
     Deliberation: Round [N]
     - Analyst: [done/pending]
     - Architect: [done/pending]
     - Senior Dev: [done/pending]
     
     Decomposition: [not started / Round N]
     
     Next: Run /breezybuilder:plan to continue.
     ```

4. **Planning complete, not executing**
   - If build-order.md exists but execution not started:
     ```
     BreezyBuilder: Ready to execute
     
     Planning:
     - Deliberation: [N] rounds
     - Decomposition: [N] rounds
     
     Build Order:
     - Phases: [N]
     - Pieces: [N]
     - Demo points: [N]
     
     Next: Run /breezybuilder:execute to start building.
     ```

5. **Execution in progress**
   - Parse build-order.md for progress:
     ```
     BreezyBuilder: Executing
     
     Progress:
     - Phase [X] of [N]: [name]
     - Piece [Y] of [M]: [name]
     - Overall: [%] complete
     
     Current piece: [name]
     - Dependencies: [list]
     - Status: [implementing/verifying/reviewing]
     
     Completed phases:
     - Phase 1: [name] ✓
     - Phase 2: [name] ✓
     
     Demo points reached: [N]
     Revisions: [N]
     
     Next: Run /breezybuilder:execute to continue.
     ```

6. **Execution complete**
   - If all pieces marked `[x]`:
     ```
     BreezyBuilder: Complete 🚀
     
     Summary:
     - Phases: [N] ✓
     - Pieces: [N] ✓
     - Demo points: [N]
     - Revisions: [N]
     
     Shipped: [timestamp]
     
     Audit trail available in .breezybuilder/
     ```

## Parse Logic

- Count `[x]` vs `[ ]` in build-order.md for progress
- Check phase "Status:" lines for phase progress
- Look for latest entry in demo-log.md for demo point status
- Count revision-* folders for revision count
