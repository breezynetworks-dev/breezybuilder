---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with required-stack.md and potential-toolbox.md configuration files.
---

# BreezyBuilder Init

Initialize BreezyBuilder for this project.

## Steps

1. **Check if already initialized**
   - If `.breezybuilder/` exists with files, inform user:
     "BreezyBuilder already initialized. Run /breezybuilder:plan to start planning, or delete .breezybuilder/ to reset."
   - Exit if already initialized

2. **Create directory structure**
   ```
   .breezybuilder/
   ├── required-stack.md
   ├── potential-toolbox.md
   ├── planning/
   └── execution/
   ```

3. **Copy default templates**
   - Read templates from the plugin's templates/ directory
   - Write required-stack.md with default stack (Next.js 15, TypeScript, Tailwind, shadcn, Drizzle, Clerk, Vercel)
   - Write potential-toolbox.md with available tools

4. **Output to user**
   ```
   ✓ BreezyBuilder initialized
   
   Created:
   - .breezybuilder/required-stack.md — Your tech stack (edit if needed)
   - .breezybuilder/potential-toolbox.md — Available tools (edit if needed)
   
   Next steps:
   1. Review and edit required-stack.md if you need different technologies
   2. Review and edit potential-toolbox.md to add/remove available tools
   3. Run /breezybuilder:plan to start planning your project
   ```

## Default required-stack.md Content

```markdown
# Required Stack

These are non-negotiable. All implementation uses this foundation.
Edit this file before running /breezybuilder:plan if you want different choices.

## Framework
- Next.js 15 (App Router)
- TypeScript (strict mode)

## Styling
- Tailwind CSS 4
- shadcn/ui components

## Database
- PostgreSQL
- Drizzle ORM

## Auth
- Clerk

## Deployment
- Vercel

---

Note: These will be installed/configured in the scaffold phase (Phase 1).
Experts do not debate these choices — they build within them.

If you need different technologies, edit this file BEFORE starting planning.
```

## Default potential-toolbox.md Content

```markdown
# Potential Toolbox

These are available tools. Use them IF the project needs them.
Not all will be used — experts recommend which apply to your specific project.

Edit this file to add/remove tools before running /breezybuilder:plan.

## Web Scraping
- Firecrawl — crawling and scraping websites, extracting structured data

## Email
- Resend — transactional email sending
- Resend Inbound — receiving and parsing incoming emails

## Payments
- Stripe — subscriptions, one-time payments, checkout
- Stripe Billing — metered/usage-based billing
- Stripe Webhooks — payment event handling

## Vector Search / Embeddings
- Qdrant — vector database for semantic search
- OpenAI Embeddings — generating embeddings for text

## AI / LLM
- Anthropic Claude API — text analysis, extraction, generation
- OpenAI API — alternative LLM provider

## Background Jobs
- Inngest — scheduled jobs, event-driven workflows, retries
- Trigger.dev — alternative background job system

## Storage
- Vercel Blob — simple file storage
- AWS S3 — scalable file storage
- Cloudflare R2 — S3-compatible storage

## Monitoring & Analytics
- Sentry — error tracking and monitoring
- PostHog — product analytics
- LogSnag — event logging and notifications

## Communication
- Slack API — sending notifications to Slack
- Discord API — sending notifications to Discord

## Data
- Postgres Full-Text Search — built-in search capability
- Redis — caching and real-time features

---

Note: 
- Experts will filter this to only relevant tools for your project
- You can add custom tools specific to your needs
- Include API documentation links if adding custom tools
```
