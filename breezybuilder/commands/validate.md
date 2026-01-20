---
description: Quick environment and connectivity check. Verifies env vars are set and services are reachable.
---

# BreezyBuilder Validate

Standalone validation command. Checks environment configuration and service connectivity without running the full build flow.

## Use Cases

- After fixing env issues — quick re-check
- Before testing — sanity check
- After deployment changes — verify new credentials
- Independent check — works even without a BreezyBuilder project

## Workflow

### Step 1: Environment Check

1. **Find required env vars:**
   - Check for `.env.example` — use as source of truth
   - If no `.env.example`, grep codebase for `process.env.` and `import.meta.env.`
   - Build list of required variables

2. **Check `.env` file exists:**
   - If missing:
     ```
     ✗ No .env file found

     Create a .env file in project root.
     ```
     Stop here.

3. **Check each required var:**
   - Is it present in `.env`?
   - Is it set (not empty)?
   - Is it not a placeholder? (detect: `your-api-key-here`, `xxx`, `changeme`, `<your-key>`, `TODO`, `REPLACE_ME`)

4. **Report status:**
   ```
   ═══════════════════════════════════════════════════════════════
   ENVIRONMENT CHECK
   ═══════════════════════════════════════════════════════════════

   .env file: ✓ found

   Variables:
   ✓ DATABASE_URL — set
   ✓ OPENAI_API_KEY — set
   ✗ STRIPE_SECRET_KEY — missing
   ✗ AUTH_SECRET — placeholder detected ("changeme")

   Status: 2 issues found
   ```

5. **If issues found:**
   ```
   Please fix the issues above and run /breezybuilder:validate again.
   ```
   Stop here (don't proceed to connectivity check with bad env).

### Step 2: Connectivity Check

Only runs if all env vars pass.

1. **Identify services:**
   - If `.breezybuilder/spec.md` exists: read `## Technical Stack`
   - Otherwise: detect from env vars and codebase:
     - `DATABASE_URL` → PostgreSQL/MySQL
     - `REDIS_URL` → Redis
     - `*_API_KEY` → likely external API

2. **For each service, run check:**

   | Service Type | Detection | Check Method |
   |--------------|-----------|--------------|
   | PostgreSQL | `DATABASE_URL` contains `postgres` | `SELECT 1` query |
   | MySQL | `DATABASE_URL` contains `mysql` | `SELECT 1` query |
   | Redis | `REDIS_URL` present | `PING` command |
   | OpenAI | `OPENAI_API_KEY` present | Models list endpoint |
   | Stripe | `STRIPE_SECRET_KEY` present | Account endpoint |
   | Generic API | `*_API_URL` pattern | HEAD request |

3. **Report status:**
   ```
   ═══════════════════════════════════════════════════════════════
   CONNECTIVITY CHECK
   ═══════════════════════════════════════════════════════════════

   Services:
   ✓ PostgreSQL — connected (23ms)
   ✓ Redis — connected (5ms)
   ✓ OpenAI API — responding (340ms)
   ✗ Stripe API — failed: Invalid API key

   Status: 1 issue found
   ```

### Step 3: Final Report

**If all passed:**
```
═══════════════════════════════════════════════════════════════
✓ VALIDATION PASSED
═══════════════════════════════════════════════════════════════

Environment: ✓ all variables set
Services: ✓ all connected

Your environment is correctly configured.
```

**If issues found:**
```
═══════════════════════════════════════════════════════════════
✗ VALIDATION FAILED
═══════════════════════════════════════════════════════════════

Issues:
- STRIPE_SECRET_KEY: Invalid API key

Please fix and run /breezybuilder:validate again.
```

## Notes

- **Fast:** Just checks env and pings services, nothing else
- **Safe:** Read-only, doesn't modify anything
- **Standalone:** Works with or without BreezyBuilder project files
- **Re-runnable:** Run as many times as needed
