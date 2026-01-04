---
description: Start BreezyBuilder planning phase. Runs intake questions, then multi-expert deliberation to determine WHAT to build, then decomposition to determine HOW to break it up. Requires .breezybuilder/ to exist (run /breezybuilder:init first).
argument-hint: <paste your project overview when prompted>
---

# BreezyBuilder Plan

Orchestrate the full planning phase: intake → deliberation → decomposition → build-order.

## Pre-flight Checks

1. Verify `.breezybuilder/` exists with required-stack.md and potential-toolbox.md
   - If missing: "Run /breezybuilder:init first"
2. Verify `.breezybuilder/design-system.md` exists
   - If missing: Warn and copy from built-in template
3. Check if planning already complete (build-order.md exists)
   - If exists: "Planning already complete. Run /breezybuilder:execute to build, or delete .breezybuilder/execution/build-order.md to re-plan"

## Phase 1: Capture Raw Overview

1. Prompt user: "Paste your project overview. This can be prose, feature lists, user stories, or any description of what you want to build. Type END on a new line when done."
2. Capture all input until END
3. Save temporarily for intake processing (will be deleted after enriched overview is created)

Note: The raw overview is preserved verbatim in the enriched overview's "Original Vision" section.

## Phase 2: Intake

The intake phase fills gaps in the overview with targeted questions—both product and technical.

### 2.1 Parse Overview

1. Invoke `breezybuilder-overview-parser` subagent
   - Pass: raw overview text, potential-toolbox.md
   - Receive: analysis with detected features, gaps, and technical questions to ask

### 2.2 Project Manager (Product Questions)

**NEW: Ask product questions before technical questions.**

1. Present transition to user:
   ```
   ═══════════════════════════════════════════════════════════════
   UNDERSTANDING YOUR PRODUCT
   ═══════════════════════════════════════════════════════════════
   
   I've analyzed your overview. To make sure I understand your 
   product correctly, I have a few questions...
   ```

2. Invoke `breezybuilder-project-manager` subagent
   - Pass: raw overview text, overview-parser analysis
   - PM asks contextual questions about:
     - Core action (what users primarily do)
     - Pages/screens that exist
     - Navigation pattern
     - Entry point after login
     - User types/roles
     - Business model
   - PM catches contradictions in real-time:
     - If user answer contradicts original overview, PM asks for clarification
     - Resolved contradictions are logged to Clarifications section
   - Receive: product details, clarifications, skipped categories

### 2.3 Ask Infrastructure Question (MANDATORY)

Always ask this question regardless of overview content:

```
═══════════════════════════════════════════════════════════════
INFRASTRUCTURE SETUP
═══════════════════════════════════════════════════════════════

How would you like to run services (database, auth, etc.)?

1. LOCAL (Recommended)
   - Everything runs on your machine
   - No accounts or API keys needed to start
   - Best for development and demos
   - Uses: Docker containers, local Postgres, etc.

2. REMOTE
   - Uses cloud services from the start
   - Requires creating accounts and API keys upfront
   - You'll need to populate .env before we can proceed

Recommendation: Start with LOCAL. You can migrate to remote 
services later when you're ready to deploy.

Your choice [1/2]:
```

Capture response.

### 2.4 Ask Conditional Technical Questions

Based on overview-parser analysis, ask questions for unspecified technical items:

- Only ask what's NOT already in the overview
- Maximum 6 conditional questions
- Skip questions where overview is explicit

Example questions (only if needed):

```
Theme preference?
1. Dark mode
2. Light mode
3. System toggle (respects OS setting)

Your choice [1/2/3]:
```

```
Your overview mentions user accounts.
Your toolbox includes: Clerk, NextAuth

Which auth provider? [Clerk/NextAuth]:
```

Collect all answers.

### 2.5 Create Environment Files (ALWAYS)

Even LOCAL mode needs API keys for services without local alternatives.

1. Determine required environment variables based on selected tools
2. Create `.env.example` in project root with local default indicators:
   ```bash
   # DATABASE — Has local default if LOCAL mode
   DATABASE_URL=postgresql://postgres:postgres@localhost:5432/app
   
   # AUTH (Clerk) — Always required, no local alternative
   NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_xxx
   CLERK_SECRET_KEY=sk_test_xxx
   
   # PAYMENTS (Stripe) — Required if payments, test keys work locally
   STRIPE_SECRET_KEY=sk_test_xxx
   STRIPE_WEBHOOK_SECRET=whsec_xxx
   NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_test_xxx
   
   # EMAIL (Resend) — Required if email features
   RESEND_API_KEY=re_xxx
   
   # APP
   NEXT_PUBLIC_APP_URL=http://localhost:3000
   ```

3. Create `.env.requirements.md` documenting:
   - Which vars have local defaults vs. always need user input
   - Links to service dashboards
   - Format expected for each var
   - Setup instructions for LOCAL vs REMOTE

4. Present setup prompt:
   ```
   ═══════════════════════════════════════════════════════════════
   ACTION REQUIRED: Environment Setup
   ═══════════════════════════════════════════════════════════════
   
   Created: .env.example (copy to .env and fill in values)
   Created: .env.requirements.md (detailed setup guide)
   
   [If LOCAL mode:]
   Some variables have working defaults (database, app URL).
   You still need API keys for external services:
   
     ✓ DATABASE_URL — Default works with Docker
     □ CLERK keys — Required (dashboard.clerk.com)
     □ STRIPE keys — Required for payments (dashboard.stripe.com)
     □ RESEND key — Required for email (resend.com)
   
   [If REMOTE mode:]
   All variables must be configured:
   
     □ DATABASE_URL — Your cloud provider
     □ CLERK keys — dashboard.clerk.com
     □ STRIPE keys — dashboard.stripe.com
     □ RESEND key — resend.com
   
   Steps:
   1. Copy .env.example to .env
   2. Get API keys from the services above
   3. Fill in the values
   4. Type READY when complete
   
   Your input:
   ```

5. Wait for "READY" (case-insensitive)
   - If user types anything else, remind them to complete setup
   - Loop until READY received

### 2.6 Verify Environment

After user types READY, verify everything works before proceeding:

```
═══════════════════════════════════════════════════════════════
VERIFYING ENVIRONMENT
═══════════════════════════════════════════════════════════════

Checking services...
```

Run verification checks based on infrastructure mode and selected tools:

| Check | When | How |
|-------|------|-----|
| Docker running | LOCAL mode | `docker info` |
| Docker services | LOCAL mode | `docker compose up -d` |
| Database connection | Always | Test connection with DATABASE_URL |
| Clerk API | If Clerk selected | API health check |
| Stripe API | If Stripe selected | API health check |
| Resend API | If Resend selected | API health check |
| Firecrawl API | If Firecrawl selected | API health check |

**On success:**
```
═══════════════════════════════════════════════════════════════
VERIFICATION COMPLETE ✓
═══════════════════════════════════════════════════════════════

✓ Docker running
✓ Docker services started (postgres)
✓ Database connection verified
✓ Clerk API key valid
✓ Stripe API key valid (test mode)

All checks passed. Proceeding to deliberation.
```

**On failure:**
```
═══════════════════════════════════════════════════════════════
VERIFICATION FAILED
═══════════════════════════════════════════════════════════════

✓ Docker running
✓ Database connection verified
✗ Clerk API key invalid
  Error: Invalid API key format
  Fix: Check CLERK_SECRET_KEY in .env
  Docs: dashboard.clerk.com → API Keys

Options:
- RETRY — Try verification again after fixing
- SKIP — Proceed anyway (will cause errors later)
- ABORT — Exit planning

Your input:
```

Handle user response:
- RETRY: Run verification again
- SKIP: Warn strongly, require "YES" confirmation, then proceed
- ABORT: Exit planning

### 2.7 Create Enriched Overview

Write to `.breezybuilder/project-overview.md`:

```markdown
# Project Overview

Generated: [timestamp]
PM Questions: [N]
Clarifications: [N]

---

## Original Vision

[RAW OVERVIEW TEXT VERBATIM — never edited]

---

## Clarifications

[Only present if PM resolved contradictions]

- "[original text]" → [corrected understanding] (PM Q[N])
- "[original text]" → [corrected understanding] (PM Q[N])

---

## Product Details

Core Action: [from PM]
Pages/Screens: [from PM]
Navigation: [from PM]
Entry Point: [from PM]
User Types: [from PM]
Business Model: [from PM]

[Additional details from PM conversation]

---

## Technical Choices

Infrastructure: [LOCAL/REMOTE]
Auth Provider: [Clerk/NextAuth/None]
Payment Provider: [Stripe/None]
Email Provider: [Resend/None]
Theme: [Dark/Light/System toggle]
Platform: [Web desktop-first / Web mobile-first / PWA]

---

## Detected Signals

[From Overview Parser]

Detected (explicitly mentioned):
- [CATEGORY]: "[quote]" (line N)

Implied (likely needed):
- [CATEGORY]: [reasoning]

Not Mentioned (asked user):
- [CATEGORY]: [user's choice]

---

## Selected Tools (from toolbox)

Based on intake, this project will use:
- [Tool 1] for [purpose]
- [Tool 2] for [purpose]

---

## Infrastructure Notes

Mode: [LOCAL/REMOTE]

[If LOCAL:]
Docker services required:
- postgres:16 (port 5432)
- redis:7 (port 6379, if needed)

[If REMOTE:]
Environment variables configured. See .env.requirements.md.

---
```

This enriched overview is now IMMUTABLE for the rest of planning.

### 2.8 Cleanup

Delete the temporary raw overview file. The "Original Vision" section in project-overview.md now contains the verbatim original input.

### 2.9 Create Deliberation File

Create empty `.breezybuilder/planning/planning-deliberation.md`:
```markdown
# Planning Deliberation

Started: [timestamp]
Project: [first line of overview or "Untitled"]
Infrastructure: [LOCAL/REMOTE]

---

```

## Phase 3: Filter Toolbox

Invoke the `breezybuilder-toolbox-filter` subagent:
- Pass: project-overview.md (enriched), potential-toolbox.md
- Receive: filtered-toolbox.md content
- Write to `.breezybuilder/filtered-toolbox.md`

Note: Toolbox filter now uses the enriched overview with intake decisions.

## Phase 4: Deliberation Loop

Track: `round = 0`, `exhaustion_count = 0`

### Loop until exhausted:

```
round += 1

Append to planning-deliberation.md:
"## Round {round}\n\n"

1. Invoke `breezybuilder-analyst` subagent
   - Pass: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md
   - Append output to planning-deliberation.md

2. Invoke `breezybuilder-architect` subagent
   - Pass: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md
   - Append output to planning-deliberation.md

3. Invoke `breezybuilder-designer` subagent
   - Pass: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md, **design-system.md**
   - Designer uses design-system.md to identify baseline patterns and propose DS-XXX refinements
   - Append output to planning-deliberation.md

4. Invoke `breezybuilder-senior-dev` subagent
   - Pass: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md
   - Append output to planning-deliberation.md

5. Check exhaustion (only if round >= 5):
   - Read latest round from planning-deliberation.md
   - If all 4 experts said "NOTHING NEW": exhaustion_count += 1
   - Else: exhaustion_count = 0
   
6. If exhaustion_count >= 1 AND round >= 5:
   - Break loop (experts exhausted)
```

## Phase 5: Synthesize Decisions

**Run BEFORE deliberation synthesizer**

1. Invoke `breezybuilder-decisions-synthesizer` subagent
   - Pass: planning-deliberation.md, planning-decisions.md (if exists from prior cycle)
   - Receive: structured decisions content (including DS-XXX from Designer)
   - Write to `.breezybuilder/planning/planning-decisions.md`

This extracts architecture decisions, integration specs, cost controls, error handling patterns, business rules, and **design standards (DS-XXX)** into a structured format for downstream agents.

## Phase 6: Synthesize & Get User Input

1. Invoke `breezybuilder-deliberation-synthesizer` subagent
   - Pass: planning-deliberation.md
   - Receive: questions, decisions, closed items

2. If questions or decisions exist:
   - Present to user in clear format
   - Wait for user responses
   - Append to planning-deliberation.md:
     ```markdown
     ---
     
     ## User Response [N]
     
     QUESTIONS:
     Q1: [user answer]
     Q2: [user answer]
     
     DECISIONS:
     D1: [confirmed/override: user's choice]
     
     ---
     ```
   - Run 5 more deliberation rounds (go back to Phase 4 loop)
   - After those rounds exhaust, run Phase 5 again (update planning-decisions.md)

3. If no questions and no decisions:
   - Deliberation complete, proceed to decomposition

## Phase 7: Decomposition

1. Create `.breezybuilder/planning/planning-decomposition.md`:
   ```markdown
   # Planning Decomposition
   
   Started: [timestamp]
   Deliberation Completed: [timestamp]
   Total Deliberation Rounds: [N]
   
   ---
   
   ```

2. Run decomposition loop (same pattern as deliberation):
   - Minimum 5 rounds
   - Same 4 experts with decomposition focus
   - Pass to Analyst, Architect, Senior Dev: required-stack.md, filtered-toolbox.md, project-overview.md, planning-deliberation.md, planning-decisions.md, planning-decomposition.md
   - **Pass to Designer: all of the above PLUS design-system.md**
   - Check exhaustion after round 5 (all 4 must say "NOTHING NEW")
   - Experts focus on: pieces, dependencies, sequence, sizing, UI components
   - Experts MUST reference planning-decisions.md for specific requirements (limits, error handling, integration specs)
   - **Designer recommends piece Types (backend/frontend/fullstack) and references DS-XXX decisions**

3. After exhaustion, invoke `breezybuilder-decomposition-synthesizer`:
   - Extract phases, pieces, demo points
   - Present to user

## Phase 8: Demo Strategy Selection

Present to user:
```
PHASES IDENTIFIED:

Phase 1: [name] — [N pieces]
Phase 2: [name] — [N pieces]
  ◆ DEMO POINT after this phase
Phase 3: [name] — [N pieces]
...
Phase N: [name] — [N pieces]
  ◆ SHIP POINT

---

SELECT DEMO STRATEGY:

1. Full build — pause only at ship point
2. Pause at every demo point for feedback  
3. Pause at specific demo points (enter phase numbers, comma-separated)

Enter 1, 2, or 3 (then phase numbers if 3):
```

Capture user selection.

## Phase 9: Write Build Order

1. Invoke `breezybuilder-build-order-writer` subagent
   - Pass: planning-deliberation.md, planning-decisions.md (including DS-XXX), planning-decomposition.md, user's demo strategy selection
   - Receive: complete build-order.md content with piece Types and decision references

2. Write to `.breezybuilder/execution/build-order.md`

3. Create empty `.breezybuilder/execution/demo-log.md`:
   ```markdown
   # Demo Log
   
   Build Started: [timestamp]
   Demo Strategy: [from build-order.md]
   Infrastructure: [LOCAL/REMOTE]
   
   ---
   
   ```

## Completion

Output to user:
```
✓ Planning complete

Infrastructure: [LOCAL/REMOTE]

Created:
- project-overview.md — enriched with product details + technical choices
- planning-deliberation.md — [N] rounds of expert deliberation
- planning-decisions.md — [N] decisions extracted (including [N] DS-XXX design standards)
- planning-decomposition.md — [N] rounds of decomposition
- build-order.md — [N] phases, [N] pieces

[If LOCAL:]
Local setup will use Docker. Make sure Docker is running before execution.

[If REMOTE:]
Using remote services. Verify .env is configured correctly.

Ready to build. Run /breezybuilder:execute to start execution.
```

## Error Handling

- If any subagent fails, log error and retry once
- If deliberation exceeds 50 rounds, warn user and ask to continue or force synthesis
- If decomposition exceeds 30 rounds, warn user and ask to continue or force synthesis
- If REMOTE selected and user never types READY, allow abort with "ABORT" keyword

## State Recovery

If command is interrupted:
- Check for existing planning files
- Resume from last complete phase
- User can run /breezybuilder:status to see current state
