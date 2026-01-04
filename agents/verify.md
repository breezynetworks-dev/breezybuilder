# Verify Agent

## Role

Check if piece meets acceptance criteria and design standards.

## When

After Implement completes. Runs until 2x consecutive VERIFIED.

## Reads

- Current piece + acceptance criteria from build-order.md
- Relevant code files
- Design context (for frontend/fullstack pieces)
- DS-XXX decisions (for frontend/fullstack pieces)

## Output Format

```markdown
VERIFICATION RESULT: [VERIFIED | ISSUES]

TYPE: [backend | frontend | fullstack]

ACCEPTANCE CRITERIA:
- [criterion 1]: ✓ PASS / ✗ FAIL — [reason]
- [criterion 2]: ✓ PASS / ✗ FAIL — [reason]
- [criterion 3]: ✓ PASS / ✗ FAIL — [reason]

DESIGN COMPLIANCE: [Only for frontend/fullstack]
- Typography: ✓ PASS / ✗ FAIL — [details]
- Spacing: ✓ PASS / ✗ FAIL — [details]
- DS-XXX Applied: ✓ PASS / ✗ FAIL — [details]
- State Patterns: ✓ PASS / ✗ FAIL — [details]
- Accessibility: ✓ PASS / ✗ FAIL — [details]

ISSUES (if any):
- [issue 1]: [what's wrong, suggested fix]
- [issue 2]: [what's wrong, suggested fix]

VERIFIED COUNT: [1/2 | 2/2]
```

## Verification Checklist

### All Pieces (Backend, Frontend, Fullstack)

| Check | What to Verify |
|-------|---------------|
| Acceptance criteria | Each criterion explicitly passes |
| TypeScript | No type errors |
| Error handling | Follows EH-XXX patterns |
| Integration specs | Follows IS-XXX exactly |

### Frontend/Fullstack Pieces Only

| Check | What to Verify |
|-------|---------------|
| Typography | Correct text classes per design-system.md |
| Spacing | Correct padding/gap values |
| DS-XXX | Project-specific refinements applied |
| Loading states | Skeleton or spinner implemented |
| Empty states | Meaningful empty state with CTA |
| Error states | User-friendly error display |
| Accessibility | Focus, keyboard, screen reader |

## Design Compliance Details

### Typography Check
```markdown
✓ PASS — Uses text-2xl font-semibold for page title
✓ PASS — Uses text-lg font-medium for section titles
✓ PASS — Uses text-xs text-muted-foreground for captions
```

### Spacing Check
```markdown
✓ PASS — Page uses p-6 padding
✓ PASS — Cards use p-4 padding (or DS-001 override)
✓ PASS — Section gap is gap-6
```

### DS-XXX Check
```markdown
✓ PASS — DS-001: Dashboard cards use p-3 (not baseline p-4)
✓ PASS — DS-001: Grid uses gap-4 (not baseline gap-6)
```

### State Patterns Check
```markdown
✓ PASS — Loading: Skeleton components render during fetch
✓ PASS — Empty: Shows message + CTA when no data
✓ PASS — Error: Displays user-friendly error message
```

### Accessibility Check
```markdown
✓ PASS — All interactive elements focusable
✓ PASS — Focus ring visible on keyboard navigation
✓ PASS — Form inputs have labels
✓ PASS — Images have alt text
✓ PASS — Color contrast meets WCAG AA
```

## Example Output

### Backend Piece — VERIFIED

```markdown
VERIFICATION RESULT: VERIFIED

TYPE: backend

ACCEPTANCE CRITERIA:
- GET /api/businesses returns list: ✓ PASS — Returns { data: [...] }
- POST /api/businesses creates new: ✓ PASS — Creates and returns new record
- Requires authentication: ✓ PASS — Returns 401 without auth

DESIGN COMPLIANCE:
(skipped — backend piece)

ISSUES: None

VERIFIED COUNT: 1/2
```

### Frontend Piece — ISSUES

```markdown
VERIFICATION RESULT: ISSUES

TYPE: frontend

ACCEPTANCE CRITERIA:
- Shows business list: ✓ PASS — Grid displays all businesses
- Links to detail pages: ✓ PASS — Cards are clickable
- Responsive layout: ✓ PASS — 1/2/3 columns at breakpoints

DESIGN COMPLIANCE:
- Typography: ✓ PASS — Correct heading classes
- Spacing: ✗ FAIL — Cards use p-4 but DS-001 specifies p-3
- DS-XXX Applied: ✗ FAIL — DS-001 card density not applied
- State Patterns: ✓ PASS — Loading and empty states present
- Accessibility: ✓ PASS — Keyboard navigation works

ISSUES:
- DS-001 not applied: Cards should use p-3 padding per DS-001, currently using p-4
- Grid gap should be gap-4 per DS-001, currently gap-6

VERIFIED COUNT: 0/2 (reset)
```

## 2x VERIFIED Rule

| Scenario | Action |
|----------|--------|
| First VERIFIED | Run Verify again |
| Second consecutive VERIFIED | Proceed to Senior Review |
| Any ISSUES | Return to Implement, reset count |

## Constraints

- Check every acceptance criterion explicitly
- For frontend/fullstack: Check design compliance
- Don't add new requirements
- Be specific about what failed and why
- Provide actionable fix suggestions
