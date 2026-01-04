---
description: Initialize BreezyBuilder in the current project. Creates .breezybuilder/ directory with required-stack.md (your universal preferences) and potential-toolbox.md (your curated tool catalog).
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
   ├── required-stack.md      ← Your universal preferences
   ├── potential-toolbox.md   ← Your curated tool catalog
   ├── planning/
   └── execution/
   ```

3. **Copy default templates**
   - Read templates from the plugin's templates/ directory
   - Write required-stack.md with universal preferences (Next.js 15, TypeScript, Tailwind, BaseUI)
   - Write potential-toolbox.md with curated tool catalog

4. **Output to user**
   ```
   ✓ BreezyBuilder initialized
   
   Created:
   - .breezybuilder/required-stack.md — Your universal preferences (framework, styling, deployment)
   - .breezybuilder/potential-toolbox.md — Your curated tool catalog (add tools you trust)
   
   These files are YOUR preferences that apply to all projects:
   
   • required-stack.md — Technologies you ALWAYS want (edit once, reuse)
   • potential-toolbox.md — Tools you trust (grow this over time)
   
   Project-specific decisions (auth, payments, etc.) are handled during 
   the intake phase when you run /breezybuilder:plan.
   
   Next steps:
   1. Review required-stack.md — Does it match your preferred tech?
   2. Review potential-toolbox.md — Add tools you've used and trust
   3. Run /breezybuilder:plan to start planning your project
   ```

## Default required-stack.md Content

```markdown
# Required Stack

These are your universal preferences that apply to ALL projects.
BreezyBuilder will use these as the foundation for every build.

Edit this file to match your personal/team tech preferences.

---

## Framework

- Next.js 15 (App Router)
- TypeScript (strict mode)

## Styling

- Tailwind CSS 4
- BaseUI components (primary)
- shadcn/ui (where BaseUI doesn't cover)

## Deployment

- Vercel

---

## Notes

- These choices are NON-NEGOTIABLE during planning
- Experts build within these constraints, they don't debate them
- Project-specific tools (auth, payments, etc.) are selected during intake
- Edit this file BEFORE running /breezybuilder:plan if you want different choices

## What Belongs Here vs. Toolbox

**Required Stack (this file):**
- Framework choices you ALWAYS want
- Styling systems you prefer
- Deployment target

**Potential Toolbox:**
- Options for auth (Clerk, NextAuth, etc.)
- Options for payments (Stripe, etc.)
- Options for email (Resend, etc.)
- Tools you might use depending on project needs

The intake phase will select from your toolbox based on project requirements.
```

## Default potential-toolbox.md Content

```markdown
# Potential Toolbox

Your curated catalog of trusted tools. Add tools here as you discover good options.
The intake phase will select from this list based on each project's needs.

---

## Authentication

### Clerk
- **Use for:** Quick setup, managed auth, social login
- **Docs:** https://clerk.com/docs

### NextAuth
- **Use for:** Self-hosted auth, more control, custom providers
- **Docs:** https://next-auth.js.org

---

## Database

### PostgreSQL + Drizzle
- **Use for:** Default choice, type-safe ORM
- **Local:** Docker postgres:16
- **Remote:** Supabase, Neon, Railway

---

## Payments

### Stripe
- **Use for:** Subscriptions, one-time, usage-based, checkout
- **Docs:** https://stripe.com/docs
- **Local:** Stripe CLI

---

## Email

### Resend
- **Use for:** Transactional email, React Email templates
- **Docs:** https://resend.com/docs

---

## Background Jobs

### Inngest
- **Use for:** Event-driven workflows, scheduled jobs, retries
- **Docs:** https://www.inngest.com/docs

---

## File Storage

### Vercel Blob
- **Use for:** Simple file storage with Vercel
- **Docs:** https://vercel.com/docs/storage/vercel-blob

### UploadThing
- **Use for:** Easy file uploads with React components
- **Docs:** https://docs.uploadthing.com

---

## AI / LLM

### Anthropic Claude API
- **Use for:** Text analysis, generation, extraction
- **Docs:** https://docs.anthropic.com

### Vercel AI SDK
- **Use for:** Streaming AI responses, multi-provider
- **Docs:** https://sdk.vercel.ai/docs

---

## Monitoring

### Sentry
- **Use for:** Error tracking, performance monitoring
- **Docs:** https://docs.sentry.io

---

## How to Add Tools

When you discover a tool you trust, add it with:
- **Use for:** When to choose this tool
- **Docs:** Link to documentation
- **Local:** How to test locally (if applicable)

The intake phase will show relevant options based on project needs.
```
