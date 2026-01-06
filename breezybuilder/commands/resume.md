---
description: Resume BreezyBuilder from last known state. Automatically detects where you left off and continues planning or execution.
---

# BreezyBuilder Resume

Resume from the last known state. Useful if a session was interrupted.

## Detection Logic

1. **Check for .breezybuilder/**
   - If missing: "Nothing to resume. Run /breezybuilder:init to start."

2. **Check execution state** (if pieces.md exists)
   - Find first `[ ]` piece
   - If found: Resume execution at that piece
   - If all `[x]`: "Build already complete. Nothing to resume."

3. **Check planning state** (if deliberation.md exists but no pieces.md)
   - Check if spec.md exists
   - If no spec: Resume at write spec
   - If spec exists: Resume at write pieces

4. **Check deliberation state** (if deliberation.md exists but incomplete)
   - Count rounds, check last round for exhaustion
   - If not exhausted: Resume deliberation loop

5. **Check for overview.md** (if exists but no deliberation)
   - Resume at deliberation phase

6. **Only preferences.md exists**
   - "Initialized but no project overview. Run /breezybuilder:plan to start planning."

## Resume Actions

### Resume Deliberation

```
Resuming deliberation...

Last state:
- Round: [N]
- Last expert: [Developer/Designer/Product]

Continuing from Round [N+1]...
```

Then run deliberation loop from current round.

### Resume Spec Writing

```
Resuming planning...

Deliberation: Complete ([N] rounds)

Writing spec...
```

Then run write-spec agent.

### Resume Pieces Writing

```
Resuming planning...

Deliberation: Complete ([N] rounds)
Spec: Written

Writing pieces...
```

Then run write-pieces agent.

### Resume Execution

```
Resuming execution...

Progress: [X]% complete
- Completed: [N] pieces
- Remaining: [M] pieces

Current piece: [Piece X.Y: name]
Type: [backend / frontend / fullstack]

Continuing...
```

Then run execution loop from current piece.

## State Recovery

Read the latest entries in each file to determine exact state:

- **preferences.md**: Check if exists
- **overview.md**: Check if exists
- **deliberation.md**: Look for last "## Round N" header
- **spec.md**: Check if exists
- **pieces.md**: Find first `[ ]` checkbox

## Output

After determining state:
1. Show current state summary
2. Ask user to confirm: "Resume from here? (Y/n)"
3. If confirmed, invoke appropriate command logic
