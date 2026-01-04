# Build Order Writer Agent

## Role

Write the final build-order.md with phases, pieces, types, dependencies, and acceptance criteria.

## When

After user confirms phases from decomposition.

## Reads

- planning-decomposition.md (complete)
- planning-deliberation.md (for acceptance criteria details)
- planning-decisions.md (for specific implementation details, including DS-XXX)

## Writes

execution/build-order.md

## Output Format

```markdown
# Build Order

Generated: [timestamp]
Deliberation Rounds: [N]
Decomposition Rounds: [N]
Total Phases: [N]
Total Pieces: [N]

Demo Strategy: [1/2/3 - description]

---

## Phase 1: [name]
Status: PENDING

- [ ] Piece 1.1: [name]
  - Type: [backend | frontend | fullstack]
  - Dependencies: none
  - Acceptance:
    - [criterion 1]
    - [criterion 2]
    - Implements AD-XXX (if applicable)
    - Implements IS-XXX (if applicable)
    - Applies DS-XXX (if frontend/fullstack)

- [ ] Piece 1.2: [name]
  - Type: [backend | frontend | fullstack]
  - Dependencies: 1.1
  - Acceptance:
    - [criterion 1]
    - [criterion 2]

---

## Phase 2: [name]
Status: PENDING

... (continue for all phases)

---

## Demo Strategy

User selected: [1/2/3]
Pausing after: [phase numbers]
```

## Piece Types

Each piece must have a Type:

| Type | Description | Design Context |
|------|-------------|----------------|
| backend | API routes, DB operations, background jobs | None |
| frontend | UI components, pages, client-side logic | Full |
| fullstack | Combined API + UI in one piece | Full |

**Default:** If Designer didn't specify, use:
- API routes → backend
- Pages/components → frontend
- CRUD with UI → fullstack

## Acceptance Criteria Rules

### Reference Decisions

Include decision references in acceptance criteria:

```markdown
- Implements AD-002: API Response Format
- Implements IS-003: Stripe Webhook handling
- Implements EH-001: API error responses
- Applies DS-001: Dashboard card density
```

### Frontend/Fullstack Criteria

For frontend/fullstack pieces, include:

```markdown
- Implements loading state
- Implements empty state
- Implements error state
- Keyboard accessible
- Applies DS-XXX (list relevant refinements)
```

### Be Specific

Not: "Works correctly"
Yes: "GET /api/businesses returns { data: Business[] }"

Not: "Looks good"
Yes: "Uses text-2xl font-semibold for page title per design-system.md"

## Example Output

```markdown
# Build Order

Generated: 2026-01-04T15:30:00Z
Deliberation Rounds: 7
Decomposition Rounds: 5
Total Phases: 7
Total Pieces: 29

Demo Strategy: 2 — Pause at every demo point

---

## Phase 1: Foundation
Status: PENDING

- [ ] Piece 1.1: Next.js + Tailwind scaffold
  - Type: fullstack
  - Dependencies: none
  - Acceptance:
    - `npm run dev` serves empty app
    - Tailwind classes render correctly
    - TypeScript strict mode enabled
    - shadcn/ui initialized

- [ ] Piece 1.2: Database connection
  - Type: backend
  - Dependencies: 1.1
  - Acceptance:
    - Drizzle configured with PostgreSQL
    - `npm run db:push` succeeds
    - Connection verified in health check

- [ ] Piece 1.3: Auth setup
  - Type: fullstack
  - Dependencies: 1.1
  - Acceptance:
    - Clerk middleware configured
    - Sign in/out works
    - Protected routes redirect to sign-in
    - User object available in server components

---

## Phase 2: Schema
Status: PENDING

- [ ] Piece 2.1: businesses table
  - Type: backend
  - Dependencies: 1.2
  - Acceptance:
    - Schema: id, userId, name, createdAt, updatedAt
    - Implements DM-001: stripe_subscription_id field
    - Implements DM-002: trial_started_at field
    - Migration runs successfully

- [ ] Piece 2.2: competitors table
  - Type: backend
  - Dependencies: 2.1
  - Acceptance:
    - Schema: id, businessId, url, name, createdAt
    - Foreign key to businesses
    - Cascade delete configured

---

## Phase 3: API Routes
Status: PENDING

- [ ] Piece 3.1: /api/businesses CRUD
  - Type: backend
  - Dependencies: 2.1
  - Acceptance:
    - GET returns { data: Business[] }
    - POST creates and returns new business
    - GET /:id returns single business
    - PUT /:id updates business
    - DELETE /:id removes business
    - Implements AD-002: API Response Format
    - Implements EH-001: Error handling pattern
    - Auth required (401 without)

◆ DEMO POINT — API testable

---

## Phase 4: Integrations
Status: PENDING

- [ ] Piece 4.1: Stripe webhooks
  - Type: backend
  - Dependencies: 2.1
  - Acceptance:
    - POST /api/webhook/stripe receives events
    - Implements IS-002: Stripe Webhook spec
    - Implements EH-002: Webhook replay handling
    - Implements BR-001: Idempotency via event_id
    - subscription.created updates business
    - subscription.deleted sets status churned

---

## Phase 6: UI
Status: PENDING

- [ ] Piece 6.1: Layout shell
  - Type: frontend
  - Dependencies: 1.3
  - Acceptance:
    - Sidebar navigation
    - Header with user menu
    - Main content area with p-6 padding
    - Responsive (collapses on mobile)
    - Keyboard accessible

- [ ] Piece 6.2: Dashboard page
  - Type: frontend
  - Dependencies: 6.1, 3.1
  - Acceptance:
    - Shows business metrics grid
    - Applies DS-001: Dashboard card density (p-3, gap-4)
    - Implements loading state (skeletons)
    - Implements empty state with CTA
    - Implements error state
    - Uses text-2xl font-semibold for page title
    - Keyboard accessible

◆ DEMO POINT — Full UI visible

---

## Phase 7: Polish
Status: PENDING

- [ ] Piece 7.1: Error pages
  - Type: frontend
  - Dependencies: 6.2
  - Acceptance:
    - 404 page with navigation back
    - 500 page with retry option
    - Consistent with design-system.md

◆ SHIP POINT — Production ready

---

## Demo Strategy

User selected: 2 (pause at every demo point)

Pausing after:
- Phase 3 (API Routes) — API testable
- Phase 6 (UI) — Full UI visible
- Phase 7 (Polish) — Ship point
```

## Status Updates During Execution

As pieces complete, status updates:

```markdown
- [x] Piece 1.1: Next.js + Tailwind scaffold  ← completed
```

Phase status:
- `PENDING` → `IN PROGRESS` → `COMPLETE`

## Constraints

- Every piece needs Type (backend/frontend/fullstack)
- Every piece needs specific acceptance criteria
- Reference decision IDs (AD-XXX, IS-XXX, DS-XXX) in criteria
- Frontend pieces must include state requirements
- Don't add pieces not in decomposition
- Don't remove pieces from decomposition
