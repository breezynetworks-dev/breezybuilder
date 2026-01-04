# Implement Agent

## Role

Build the current piece according to acceptance criteria, using design patterns and decisions.

## When

Each piece in build-order.md, after Code Selector prepares context.

## Reads

| Content | Source | Tokens |
|---------|--------|--------|
| Stack | required-stack.md | ~500 |
| Current piece | build-order.md (piece section only) | ~200 |
| Decision sections | planning-decisions.md (filtered) | ~500-1000 |
| Design context | design-system.md (filtered, if frontend/fullstack) | ~500-800 |
| Relevant code | From Code Selector | ~5-15k |

**Total:** ~10-20k tokens (well under 80k limit)

## Does NOT Read

- planning-deliberation.md (archived)
- planning-decomposition.md (archived)
- demo-log.md (not needed)
- Other pieces in build-order.md
- project-overview.md (distilled into decisions)

## Context Structure

```markdown
## Required Stack

[contents of required-stack.md]

---

## Current Piece

Piece X.Y: [name]
Type: [backend | frontend | fullstack]

Dependencies: A.B, C.D

Acceptance:
- [criterion 1]
- [criterion 2]
- [criterion 3]

---

## Relevant Decisions

### AD-XXX: [name]
[decision details]

### IS-XXX: [name]
[integration spec]

### DS-XXX: [name]
[design refinement — only for frontend/fullstack]

---

## Design Context
[Only included for frontend/fullstack pieces]

### Typography
[relevant sections]

### Spacing
[relevant sections]

### Patterns
[relevant layout/component/state patterns]

---

## Relevant Code

### path/to/file.ts
```typescript
[file contents]
```

### path/to/other.ts
```typescript
[file contents]
```
```

## Behavior

### For Backend Pieces

1. Read acceptance criteria
2. Check relevant decisions (AD-XXX, IS-XXX, EH-XXX)
3. Implement following patterns from decisions
4. Write clean, typed code
5. Handle errors per EH-XXX patterns

### For Frontend/Fullstack Pieces

1. Read acceptance criteria
2. Check relevant decisions including DS-XXX
3. **Apply design-system.md patterns:**
   - Use specified typography classes
   - Use specified spacing values
   - Follow layout patterns
   - Implement state patterns (loading, empty, error)
4. **Apply DS-XXX refinements** (override baseline when specified)
5. Use shadcn/ui components
6. Ensure accessibility

## Design System Application

### Typography
```tsx
// Page title
<h1 className="text-2xl font-semibold">Dashboard</h1>

// Section title
<h2 className="text-lg font-medium">Recent Activity</h2>

// Body text
<p className="text-sm">Content here</p>

// Caption
<span className="text-xs text-muted-foreground">Updated 2 hours ago</span>
```

### Spacing
```tsx
// Page layout
<div className="p-6">
  <div className="flex flex-col gap-6">
    {/* sections with gap-6 */}
  </div>
</div>

// Card
<Card className="p-4">
  <div className="flex flex-col gap-4">
    {/* elements with gap-4 */}
  </div>
</Card>
```

### DS-XXX Override Example

If DS-001 says "Dashboard Card Density: p-3, gap-4":
```tsx
// Use DS-001 instead of baseline
<Card className="p-3">  {/* DS-001: tighter padding */}
  ...
</Card>
<div className="grid gap-4">  {/* DS-001: tighter gap */}
  ...
</div>
```

## Output

Code files that implement the piece:
- Create new files as needed
- Modify existing files if extending
- Follow project structure conventions

## Quality Checklist

Before completing:

### All Pieces
- [ ] Meets all acceptance criteria
- [ ] Follows AD-XXX architecture decisions
- [ ] Implements IS-XXX integration specs
- [ ] Handles errors per EH-XXX patterns
- [ ] TypeScript strict mode passes
- [ ] No console.log (use proper logging)

### Frontend/Fullstack Pieces
- [ ] Uses design-system.md typography
- [ ] Uses design-system.md spacing
- [ ] Follows layout patterns
- [ ] Implements loading states
- [ ] Implements empty states
- [ ] Implements error states
- [ ] Applies DS-XXX refinements
- [ ] Uses shadcn/ui components correctly
- [ ] Keyboard accessible
- [ ] Screen reader friendly

## Example Implementation

### Backend Piece

```typescript
// app/api/businesses/route.ts
import { db } from '@/lib/db'
import { businesses } from '@/db/schema'
import { auth } from '@clerk/nextjs'
import { NextResponse } from 'next/server'

export async function GET() {
  try {
    const { userId } = auth()
    if (!userId) {
      return NextResponse.json(
        { error: 'Unauthorized' },  // AD-002: API Response Format
        { status: 401 }
      )
    }
    
    const data = await db
      .select()
      .from(businesses)
      .where(eq(businesses.userId, userId))
    
    return NextResponse.json({ data })  // AD-002: API Response Format
  } catch (error) {
    console.error('Failed to fetch businesses:', error)  // EH-001: Error Logging
    return NextResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }
}
```

### Frontend Piece

```tsx
// app/dashboard/page.tsx
import { Card } from '@/components/ui/card'
import { Skeleton } from '@/components/ui/skeleton'

export default function DashboardPage() {
  const { data, isLoading, error } = useBusinesses()
  
  if (error) {
    return <ErrorState message="Failed to load dashboard" />  // State pattern
  }
  
  return (
    <div className="p-6">  {/* design-system: Page padding */}
      <h1 className="text-2xl font-semibold mb-6">Dashboard</h1>  {/* Typography */}
      
      {isLoading ? (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">  {/* DS-001 */}
          {[...Array(6)].map((_, i) => (
            <Skeleton key={i} className="h-32" />  {/* Loading state */}
          ))}
        </div>
      ) : data.length === 0 ? (
        <EmptyState   {/* Empty state pattern */}
          icon={<Building2 />}
          title="No businesses yet"
          description="Get started by adding your first business."
          action={<Button>Add Business</Button>}
        />
      ) : (
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">  {/* DS-001 */}
          {data.map(business => (
            <Card key={business.id} className="p-3">  {/* DS-001: tighter padding */}
              <h3 className="text-lg font-medium">{business.name}</h3>  {/* Typography */}
              <p className="text-xs text-muted-foreground">  {/* Typography: caption */}
                {business.createdAt}
              </p>
            </Card>
          ))}
        </div>
      )}
    </div>
  )
}
```

## Constraints

- Stay within acceptance criteria scope
- Don't add features not in the piece
- Don't modify files outside piece scope
- Follow required-stack.md choices exactly
- Apply design-system.md patterns (frontend/fullstack)
- Apply DS-XXX refinements when specified
