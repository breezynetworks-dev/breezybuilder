# Code Selector Agent

## Role

Select relevant code files and design context for the current piece. Keep Implement agent context lean.

## When

Before each Implement agent spawns.

## Reads

- build-order.md (current piece section only, including Type)
- planning-decisions.md (to identify relevant sections)
- design-system.md (for frontend/fullstack pieces)
- Project file structure (via Glob/find)

## Outputs

List of files and context to load for Implement agent:
- Code files relevant to current piece
- Decision sections (AD-XXX, IS-XXX, etc.)
- Design context (based on piece Type)

## Output Format

```markdown
CURRENT PIECE: Piece X.Y — [name]
TYPE: [backend | frontend | fullstack]

DEPENDENCIES: Piece A.B (description), Piece C.D (description)

CODE FILES TO LOAD:
- path/to/file.ts (dependency)
- path/to/file.ts (will extend)
- path/to/types.ts (shared types)

DECISION SECTIONS TO LOAD:
- AD-XXX: [name]
- IS-XXX: [name]
- DS-XXX: [name] (if frontend/fullstack)

DESIGN CONTEXT TO LOAD:
[Only for frontend/fullstack pieces]
- Typography: [relevant section]
- Spacing: [relevant section]
- Layout Patterns: [which patterns apply]
- Component Patterns: [which patterns apply]
- State Patterns: [which patterns apply]

ESTIMATED TOKENS: ~N

FILES EXCLUDED:
- [category]: [reason]
```

## Design Context Filtering

Based on piece Type, load appropriate design context:

| Type | Design Context | Tokens |
|------|---------------|--------|
| backend | Skip entirely | 0 |
| frontend | Load relevant sections | ~500-800 |
| fullstack | Load relevant sections | ~500-800 |

### What to Load for Frontend/Fullstack

Based on what the piece builds, load only relevant sections from design-system.md:

| Piece Builds | Load From design-system.md |
|--------------|---------------------------|
| Form | Forms, Inputs, Buttons, Error States |
| List/Table | Data Table, Stack Layout, Empty States |
| Cards | Card Grid, Cards, Loading States |
| Dialog/Modal | Dialogs, Buttons |
| Page layout | Page Layout, Typography |
| Dashboard | Card Grid, Typography, Loading States |

### DS-XXX Decisions

Always load DS-XXX decisions from planning-decisions.md that apply to the current piece:

```markdown
DESIGN CONTEXT TO LOAD:
- DS-001: Dashboard Card Density (applies to this piece)
- DS-003: Form Field Spacing (applies to this piece)
```

## Logic

```python
def select_context(piece):
    files = []
    decisions = []
    design_context = []
    
    # 1. Trace code dependencies
    for dep in piece.dependencies:
        files.extend(find_files_for_piece(dep))
    
    # 2. Add files this piece will create/modify
    files.extend(infer_files_for_piece(piece))
    
    # 3. Add shared types/utils if referenced
    files.extend(find_shared_dependencies(files))
    
    # 4. Select relevant decision sections
    decisions = find_relevant_decisions(piece)
    
    # 5. Select design context based on Type
    if piece.type in ['frontend', 'fullstack']:
        design_context = filter_design_sections(piece, design_system_md)
        decisions.extend(find_ds_xxx_decisions(piece))
    
    # 6. Estimate tokens
    tokens = estimate_tokens(files, decisions, design_context)
    
    return {
        'files': files,
        'decisions': decisions,
        'design_context': design_context,
        'tokens': tokens
    }
```

## Example Output

### Backend Piece

```markdown
CURRENT PIECE: Piece 3.1 — /api/businesses CRUD
TYPE: backend

DEPENDENCIES: Piece 2.1 (businesses table)

CODE FILES TO LOAD:
- db/schema/businesses.ts (dependency)
- lib/db.ts (database connection)
- types/index.ts (shared types)

DECISION SECTIONS TO LOAD:
- AD-002: API Response Format
- EH-001: API Error Handling

DESIGN CONTEXT TO LOAD:
(skipped — backend piece)

ESTIMATED TOKENS: ~3,000

FILES EXCLUDED:
- All UI components (not relevant)
- Other API routes (not relevant)
```

### Frontend Piece

```markdown
CURRENT PIECE: Piece 6.2 — Dashboard page
TYPE: frontend

DEPENDENCIES: Piece 6.1 (Layout shell), Piece 3.1 (/api/businesses)

CODE FILES TO LOAD:
- components/layout/shell.tsx (dependency)
- lib/api/businesses.ts (data fetching)
- types/business.ts (types)

DECISION SECTIONS TO LOAD:
- AD-005: Dashboard Layout
- DS-001: Dashboard Card Density
- DS-002: Metric Display Format

DESIGN CONTEXT TO LOAD:
- Typography: Page title, Section title, Caption
- Spacing: Page padding, Card padding, Section gap
- Layout Patterns: Card Grid
- Component Patterns: Cards
- State Patterns: Loading States, Empty States

ESTIMATED TOKENS: ~8,000

FILES EXCLUDED:
- API route implementations (not relevant)
- Other pages (not relevant)
- Background jobs (not relevant)
```

## Guardrails

| Condition | Action |
|-----------|--------|
| > 30 files selected | Warn that piece may be too large |
| > 20k tokens estimated | Warn and suggest splitting |
| Missing dependency files | Error and list what's missing |
| No Type specified | Default to fullstack |

## Token Budgets

| Component | Target |
|-----------|--------|
| Code files | 5-15k |
| Decision sections | 500-1000 |
| Design context | 0 (backend) / 500-800 (frontend) |
| **Total** | **10-20k** |

Well under 80k limit. Leaves room for Implement agent reasoning.
