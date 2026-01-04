# Defaults

Your typical choices by project type. Intake uses these as starting points,
then asks clarifying questions based on the project overview.

These are **preferences**, not mandates. Intake can override based on project needs.

---

## Web App (default assumption)

When the overview describes a web application, dashboard, SaaS, etc.

**Framework:**
- Next.js 15 (App Router)
- TypeScript (strict mode)

**Styling:**
- Tailwind CSS 4

**Deployment:**
- Vercel

---

## Python

When the overview mentions Python, FastAPI, Django, Flask, etc.

**Framework:**
- FastAPI (default) or Django/Flask if specified

**Package Manager:**
- Poetry

**Deployment:**
- Ask during intake

---

## CLI Tool

When the overview describes a command-line tool.

**Language:**
- Ask during intake (Node, Python, Go, Rust, etc.)

---

## Detection Hints

Intake uses these signals to detect project type:

| If overview mentions... | Assume project type |
|------------------------|---------------------|
| "dashboard", "SaaS", "web app", "website", "frontend" | Web App |
| "Flask", "FastAPI", "Django", "Python", ".py" | Python |
| "CLI", "command line", "terminal", "script" | CLI Tool |
| "API only", "backend service", "microservice" | Backend Service |
| None of the above | Ask during intake |

---

## What Belongs Here vs. Toolbox

**Defaults (this file):**
- Framework preferences by project type
- Language preferences
- Deployment targets

**Potential Toolbox:**
- Component libraries (shadcn + BaseUI, shadcn + Radix)
- Auth options (Clerk, NextAuth)
- Payment options (Stripe)
- Database options (Postgres, Supabase)
- Email options (Resend, Loops)
- All other tools you might use

**The intake phase selects specific tools from your toolbox based on project requirements.**

---

## How This Works

```
1. You paste your project overview
2. Overview Parser detects project type
3. Intake loads defaults for that type
4. Intake asks about tools based on detected needs
5. Results go into project-overview.md as "Technical Choices"
6. Experts build within those choices
```

---

## Customizing

Edit this file to match your preferences:

- Add project types you commonly build
- Set your preferred frameworks
- Add detection hints for your patterns

Future projects will use your updated defaults automatically.
