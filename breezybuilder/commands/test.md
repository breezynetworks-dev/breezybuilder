---
description: Run comprehensive UI testing. Browses app like a user, finds issues, fixes them. Repeats until perfect.
---

# BreezyBuilder Test

Automated frontend testing that browses your app like a user would, identifies issues, and fixes them.

## Overview

This command runs iterative test passes until no issues are found:

```
Pass 1: Browse app → fix issues found
Pass 2: Browse app → fix remaining issues
Pass 3: Browse app → fix any new issues
...
Pass N: Browse app → NO ISSUES FOUND → Done
```

Like deliberation, it continues until "nothing to fix."

## Prerequisites

- Project must be built (all pieces complete)
- App must be running (dev server started)
- `.breezybuilder/spec.md` must exist

## Context Loaded

Each test pass receives:
- `spec.md` — what features to test
- `preferences.md` — design rules to enforce
- `test-history.md` — what previous passes found/fixed

## Workflow

### Step 1: Verify App Running

```
═══════════════════════════════════════════════════════════════
BREEZYBUILDER TEST
═══════════════════════════════════════════════════════════════

Before testing, ensure your app is running.

What is the URL to test? (default: http://localhost:3000)
```

Verify URL is accessible. If not:
```
Cannot reach [URL]. Please start your dev server and press Enter.
```

### Step 2: Initialize Test History

**If `.breezybuilder/test-history.md` doesn't exist**, create it:

```markdown
# Test History

## Session: [timestamp]

(starting first pass)
```

**If it already exists** (from previous test runs), append a new session:

```markdown
---

## Session: [timestamp]

(starting first pass)
```

This preserves history across multiple test runs.

### Step 3: Run Test Passes

Loop until completion:

```
═══════════════════════════════════════════════════════════════
TEST PASS [N]
═══════════════════════════════════════════════════════════════

Starting fresh test pass...
```

1. Invoke `breezybuilder-test-runner` subagent:
   - Pass: spec.md, preferences.md, test-history.md, app URL
   - Agent browses entire app as user
   - Agent fixes issues using frontend-design skill
   - Agent returns: `ISSUES_FIXED: [N]` or `NO_ISSUES_FOUND`

2. Update test-history.md with pass results

3. Check result:
   - If `ISSUES_FIXED: N` → continue to next pass
   - If `NO_ISSUES_FOUND` → testing complete

### Step 4: Pass Complete

After each pass:

```
═══════════════════════════════════════════════════════════════
PASS [N] COMPLETE
═══════════════════════════════════════════════════════════════

Issues found and fixed: [N]
- [brief description of each fix]

Starting next pass to verify fixes and catch remaining issues...
```

### Step 5: Testing Complete

When a pass finds no issues:

```
═══════════════════════════════════════════════════════════════
✓ TESTING COMPLETE
═══════════════════════════════════════════════════════════════

Passes run: [N]
Total issues fixed: [N]

Your app has been tested and all issues resolved.

Summary:
- Pass 1: Fixed [N] issues
- Pass 2: Fixed [N] issues
- ...
- Pass [N]: No issues found

Test history saved to .breezybuilder/test-history.md
```

## Test History File

`.breezybuilder/test-history.md` tracks all sessions and passes:

```markdown
# Test History

## Session: 2026-01-15 14:30

Completed: 2026-01-15 14:45
Passes: 3
Issues fixed: 7

### Pass 1
Issues found: 5

Fixes:
- Dashboard: Added missing loading skeleton (src/app/dashboard/page.tsx)
- Navbar: Fixed incorrect text color (src/components/Navbar.tsx)
- Button: Corrected spacing (src/components/ui/button.tsx)
- Form: Added error state styling (src/app/settings/page.tsx)
- Card: Fixed hover effect (src/components/BusinessCard.tsx)

### Pass 2
Issues found: 2

Fixes:
- Dashboard: Loading skeleton was wrong size (src/app/dashboard/page.tsx)
- Modal: Close button not visible on mobile (src/components/Modal.tsx)

### Pass 3
Issues found: 0

Result: NO_ISSUES_FOUND

---

## Session: 2026-01-18 09:15

(After adding new Settings feature)

Completed: 2026-01-18 09:25
Passes: 2
Issues fixed: 3

### Pass 1
Issues found: 3

Fixes:
- Settings: Missing form validation styling (src/app/settings/page.tsx)
- Settings: Wrong button variant (src/app/settings/page.tsx)
- Sidebar: Settings link missing active state (src/components/Sidebar.tsx)

### Pass 2
Issues found: 0

Result: NO_ISSUES_FOUND
```

## Handoff (Context Management)

If a test pass exceeds 20 fix iterations within a single pass:

1. Write handoff to test-history.md
2. Spawn fresh test-runner to continue
3. New runner reads history and continues

This prevents context bloat during passes with many issues.

## Maximum Passes

Safety limit: 10 passes maximum.

If after 10 passes issues are still being found:

```
═══════════════════════════════════════════════════════════════
⚠ TESTING LIMIT REACHED
═══════════════════════════════════════════════════════════════

10 passes completed but issues are still being found.
This may indicate a deeper problem.

Last pass found: [N] issues

Options:
[Continue] — run more passes
[Stop] — end testing, review test-history.md manually

```

## Error Handling

**App not accessible:**
- Prompt user to start dev server
- Re-check URL
- Continue when accessible

**Test runner fails:**
- Log error
- Retry pass once
- If still failing, pause for user decision

**Fix causes new issues:**
- Next pass will catch them
- This is expected and handled by iterative approach
