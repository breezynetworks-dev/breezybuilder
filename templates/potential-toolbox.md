# Potential Toolbox

Your curated catalog of trusted tools. Add tools here as you discover good options.
The intake phase will select from this list based on each project's needs.

---

## Component Libraries

UI component primitives and styling systems.

### shadcn/ui + BaseUI
- **Use for:** Headless BaseUI primitives with shadcn styling
- **Docs:** https://ui.shadcn.com, https://base-ui.com
- **Best for:** Full control, accessible by default

### shadcn/ui + Radix
- **Use for:** Headless Radix primitives with shadcn styling
- **Docs:** https://ui.shadcn.com, https://www.radix-ui.com
- **Best for:** Most common choice, great documentation

### No component library
- **Use for:** Simple projects, custom components only
- **Best for:** Landing pages, minimal UI

---

## Authentication

Tools for user auth and session management.

### Clerk
- **Use for:** Quick setup, managed auth, social login
- **Docs:** https://clerk.com/docs
- **Pricing:** Free tier available, then per-MAU

### NextAuth
- **Use for:** Self-hosted auth, more control, custom providers
- **Docs:** https://next-auth.js.org
- **Pricing:** Free (self-hosted)

---

## Database

Database options and ORMs.

### PostgreSQL + Drizzle
- **Use for:** Default choice, type-safe ORM
- **Local:** Docker postgres:16
- **Remote:** Supabase, Neon, Railway, Vercel Postgres

### Supabase
- **Use for:** Postgres + realtime + auth + storage bundle
- **Docs:** https://supabase.com/docs
- **Pricing:** Free tier available

### SQLite + Drizzle
- **Use for:** Simple projects, embedded database
- **Local:** File-based, no Docker needed
- **Best for:** CLI tools, small apps

---

## Payments

Payment processing and billing.

### Stripe
- **Use for:** Subscriptions, one-time, usage-based, checkout
- **Docs:** https://stripe.com/docs
- **Local testing:** Stripe CLI (`stripe listen`)
- **Pricing:** ~2.9% + $0.30 per transaction

---

## Email

Transactional and marketing email.

### Resend
- **Use for:** Transactional email, React Email templates
- **Docs:** https://resend.com/docs
- **Local testing:** Test mode (logs to console)
- **Pricing:** Free tier (100 emails/day)

### Loops
- **Use for:** Marketing email, drip campaigns
- **Docs:** https://loops.so/docs

---

## Background Jobs

Scheduled tasks and async processing.

### Inngest
- **Use for:** Event-driven workflows, scheduled jobs, retries
- **Docs:** https://www.inngest.com/docs
- **Local:** inngest dev server
- **Pricing:** Free tier available

### Trigger.dev
- **Use for:** Background jobs, long-running tasks
- **Docs:** https://trigger.dev/docs

---

## File Storage

File uploads and media storage.

### Vercel Blob
- **Use for:** Simple file storage with Vercel
- **Docs:** https://vercel.com/docs/storage/vercel-blob

### Cloudflare R2
- **Use for:** S3-compatible, no egress fees
- **Docs:** https://developers.cloudflare.com/r2

### UploadThing
- **Use for:** Easy file uploads with React components
- **Docs:** https://docs.uploadthing.com

---

## AI / LLM

AI and language model integrations.

### Anthropic Claude API
- **Use for:** Text analysis, generation, extraction
- **Docs:** https://docs.anthropic.com
- **Models:** Claude 3.5 Sonnet, Claude 3 Opus

### OpenAI API
- **Use for:** GPT models, embeddings, vision
- **Docs:** https://platform.openai.com/docs

### Vercel AI SDK
- **Use for:** Streaming AI responses, multi-provider support
- **Docs:** https://sdk.vercel.ai/docs

---

## Vector Search / Embeddings

Semantic search and embeddings storage.

### Qdrant
- **Use for:** Vector database, semantic search
- **Local:** Docker qdrant/qdrant
- **Docs:** https://qdrant.tech/documentation

### Pinecone
- **Use for:** Managed vector database
- **Docs:** https://docs.pinecone.io

---

## Web Scraping

Crawling and scraping websites.

### Firecrawl
- **Use for:** Web scraping, structured extraction
- **Docs:** https://docs.firecrawl.dev
- **Pricing:** Pay per page crawled

---

## Monitoring & Analytics

Error tracking and product analytics.

### Sentry
- **Use for:** Error tracking, performance monitoring
- **Docs:** https://docs.sentry.io

### PostHog
- **Use for:** Product analytics, feature flags, session replay
- **Docs:** https://posthog.com/docs

### LogSnag
- **Use for:** Event logging, notifications
- **Docs:** https://docs.logsnag.com

---

## Communication

Notifications to external services.

### Slack API
- **Use for:** Sending notifications to Slack channels
- **Docs:** https://api.slack.com/docs

### Discord API
- **Use for:** Bot notifications, webhooks
- **Docs:** https://discord.com/developers/docs

---

## Caching

In-memory caching and real-time features.

### Redis
- **Use for:** Caching, sessions, rate limiting, pub/sub
- **Local:** Docker redis:7
- **Remote:** Upstash, Redis Cloud

---

## Python-Specific Tools

For Python projects.

### FastAPI
- **Use for:** Modern Python web framework, async, auto-docs
- **Docs:** https://fastapi.tiangolo.com

### Poetry
- **Use for:** Python dependency management
- **Docs:** https://python-poetry.org/docs

### SQLAlchemy
- **Use for:** Python ORM, flexible and powerful
- **Docs:** https://docs.sqlalchemy.org

### Celery
- **Use for:** Python background tasks, distributed processing
- **Docs:** https://docs.celeryq.dev

---

## How to Add Tools

When you discover a tool you trust, add it here with:

```markdown
### Tool Name
- **Use for:** When to choose this tool
- **Docs:** Link to documentation
- **Local testing:** How to test locally (if applicable)
- **Pricing:** Cost info
```

The intake phase will show relevant options from this list based on what the project needs.
