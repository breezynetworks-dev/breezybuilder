---
description: Resume BreezyBuilder from last known state. Automatically detects where you left off and continues planning or execution.
---

# BreezyBuilder Resume

Resume from the last known state. Useful if a session was interrupted.

## Detection Logic

1. **Check for .breezybuilder/**
   - If missing: "Nothing to resume. Run /breezybuilder:init to start."

2. **Check execution state** (if build-order.md exists)
   - Find first `[ ]` piece
   - If found: Resume execution at that piece
   - If all `[x]`: "Build already complete. Nothing to resume."

3. **Check decomposition state** (if planning-decomposition.md exists but no build-order.md)
   - Check if decomposition exhausted
   - If not: Resume decomposition loop
   - If exhausted but no build-order: Generate build-order

4. **Check deliberation state** (if planning-deliberation.md exists but no decomposition)
   - Count rounds, check last round for exhaustion
   - If not exhausted: Resume deliberation loop
   - If exhausted: Run synthesizer, proceed to decomposition

5. **Check for project-overview.md** (if exists but no deliberation)
   - Resume at toolbox filter → deliberation

6. **Only required-stack.md and potential-toolbox.md**
   - "Initialized but no project overview. Run /breezybuilder:plan to start planning."

## Resume Actions

### Resume Deliberation

```
Resuming deliberation...

Last state:
- Round: [N]
- Last expert: [Analyst/Architect/Senior Dev]

Continuing from Round [N+1]...
```

Then run deliberation loop from current round.

### Resume Decomposition

```
Resuming decomposition...

Deliberation: Complete ([N] rounds)
Decomposition: Round [M]

Continuing from Round [M+1]...
```

Then run decomposition loop from current round.

### Resume Execution

```
Resuming execution...

Progress: [X]% complete
- Completed: [N] pieces
- Remaining: [M] pieces

Current piece: [Piece X.Y: name]
Status: [last known status]

Continuing...
```

Then run execution loop from current piece.

## State Recovery

Read the latest entries in each file to determine exact state:

- **planning-deliberation.md**: Look for last "## Round N" header
- **planning-decomposition.md**: Look for last "## Round N" header
- **build-order.md**: Find first `[ ]` checkbox
- **demo-log.md**: Check for incomplete demo point entries

## Output

After determining state:
1. Show current state summary
2. Ask user to confirm: "Resume from here? (Y/n)"
3. If confirmed, invoke appropriate command logic (/breezybuilder:plan or /breezybuilder:execute)
