# Preferences

Your preferences for how projects should be built. Three sections, one for each expert.

---

## Developer

Your technical preferences, stack choices, and tools.

### Philosophy
- Simple over clever
- Minimal dependencies
- Type safety always
- Monolith until proven otherwise

### Stack Defaults

**Web App:**
- Framework: Next.js 15 (App Router)
- Language: TypeScript (strict mode)
- Styling: Tailwind CSS 4
- Deployment: Vercel

**Python:**
- Framework: FastAPI
- Package Manager: Poetry

**CLI Tool:**
- Ask during intake

### Tools I Like

#### Component Libraries
- **shadcn/ui + Radix** — Headless Radix primitives with shadcn styling. Best for most projects.
- **shadcn/ui + BaseUI** — Headless BaseUI primitives with shadcn styling. Full control.
- **No component library** — Custom components only. For simple landing pages.

#### Database
- **PostgreSQL + Drizzle** — Default choice, type-safe ORM
  - Local: Docker postgres:16
  - Remote: Supabase, Neon, Railway, Vercel Postgres
- **SQLite + Drizzle** — Simple projects, embedded database, no Docker needed

#### Authentication
- **Clerk** — Quick setup, managed auth, social login. Free tier available.
- **NextAuth** — Self-hosted auth, more control, custom providers. Free.

#### Payments
- **Stripe** — Subscriptions, one-time, usage-based, checkout
  - Local testing: Stripe CLI (`stripe listen`)
  - Pricing: ~2.9% + $0.30 per transaction

#### Email
- **Resend** — Transactional email, React Email templates. Free tier (100/day).
- **Loops** — Marketing email, drip campaigns

#### Background Jobs
- **Inngest** — Event-driven workflows, scheduled jobs, retries. Free tier.
- **Trigger.dev** — Background jobs, long-running tasks

#### File Storage
- **Vercel Blob** — Simple file storage with Vercel
- **Cloudflare R2** — S3-compatible, no egress fees
- **UploadThing** — Easy file uploads with React components

#### AI / LLM
- **Anthropic Claude API** — Text analysis, generation, extraction
- **OpenAI API** — GPT models, embeddings, vision
- **Vercel AI SDK** — Streaming AI responses, multi-provider support

#### Vector Search
- **Qdrant** — Vector database, semantic search. Local: Docker qdrant/qdrant
- **Pinecone** — Managed vector database

#### Monitoring
- **Sentry** — Error tracking, performance monitoring
- **PostHog** — Product analytics, feature flags, session replay

#### Caching
- **Redis** — Caching, sessions, rate limiting, pub/sub
  - Local: Docker redis:7
  - Remote: Upstash, Redis Cloud

---

## Designer

Your visual and UX preferences.

### Philosophy
- Minimal chrome, content-first
- Subtle interactions over flashy animations
- Dark mode default
- Information density over excessive whitespace

### Typography

| Element | Classes | Usage |
|---------|---------|-------|
| Page title | `text-2xl font-semibold` | Main heading per page |
| Section title | `text-lg font-medium` | Card headers, sections |
| Body | `text-sm` | Primary content |
| Caption | `text-xs text-muted-foreground` | Secondary info, timestamps |
| Code | `font-mono text-sm` | Inline code |

### Spacing

| Context | Value | Example |
|---------|-------|---------|
| Page padding | `p-6` | Main content area |
| Card padding | `p-4` | Inside cards |
| Section gap | `gap-6` | Between major sections |
| Element gap | `gap-4` | Between form fields |
| Tight gap | `gap-2` | Label to input |
| Inline spacing | `gap-1` | Icon to text |

### Layout Patterns

**Page Layout:**
```tsx
<div className="max-w-6xl mx-auto p-6">
  <header className="mb-6">
    <h1 className="text-2xl font-semibold">Page Title</h1>
  </header>
  <main>
    {/* content */}
  </main>
</div>
```

**Card Grid:**
```tsx
<div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  {items.map(item => <Card key={item.id} />)}
</div>
```

**Stack Layout:**
```tsx
<div className="flex flex-col gap-4">
  {items.map(item => <Item key={item.id} />)}
</div>
```

### Component Patterns

**Forms:**
- Label above input
- Error message below input
- Submit button right-aligned (or full width on mobile)
- Use shadcn Form with react-hook-form + zod

**Buttons:**
| Variant | Usage |
|---------|-------|
| Primary | Main action |
| Secondary | Secondary action |
| Destructive | Delete, remove |
| Ghost | Tertiary, icon-only |

**Dialogs:**
- Title + description in header
- Actions in footer (Cancel left, Primary right)

### State Patterns

**Loading:**
- Skeleton for content areas
- Spinner for buttons
- Progress for operations

**Empty:**
- Centered message with optional CTA
- Icon + title + description + button

**Error:**
- Form errors: inline below field
- Toast for action errors
- Full page with retry for page errors

### Accessibility

| Requirement | Implementation |
|-------------|----------------|
| Focus visible | Ring on focus (shadcn default) |
| Color contrast | WCAG AA (4.5:1 text, 3:1 UI) |
| Keyboard navigation | All interactive elements focusable |
| Screen readers | Meaningful alt text, ARIA labels |
| Form labels | Every input has associated label |

### Motion

| Element | Transition |
|---------|------------|
| Hover states | `transition-colors` (150ms) |
| Dialogs | Fade + scale |
| Loading | `animate-pulse` for skeletons |
| Spinners | `animate-spin` |

---

## Product

Your product and business preferences.

### Philosophy
- Ship fast, iterate
- Fewer features done well
- Always have an escape hatch for users
- Explicit over magic

### Project Type Detection

| If overview mentions... | Assume type |
|------------------------|-------------|
| "dashboard", "SaaS", "web app", "frontend" | Web App |
| "Flask", "FastAPI", "Django", "Python" | Python |
| "CLI", "command line", "terminal", "script" | CLI Tool |
| "API only", "backend service", "microservice" | Backend Service |
| None of the above | Ask during intake |

### Questions I Care About
- What's the core action users take?
- What pages/screens exist?
- How do users navigate?
- Where do they land after login?
- Are there different user types/roles?
- What's the business model?

### Scope Preferences
- Start with MVP, add later
- Avoid premature optimization
- Build for the happy path first, then edge cases
- Prefer explicit configuration over convention

---

## How This Works

1. You paste your project overview
2. Experts read their section of this file
3. They ask questions based on gaps between your overview and preferences
4. Answers + discussion → spec.md (resolved requirements)
5. Pieces are built following your preferences

Edit this file to match your style. Future projects use your updated preferences.
