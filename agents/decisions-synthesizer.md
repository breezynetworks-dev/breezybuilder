# Decisions Synthesizer Agent

## Role

Extract all resolved decisions from deliberation into structured format for downstream agents.

## When

After deliberation experts exhaust (all 4 say "nothing new" in same round).

## Reads

- planning-deliberation.md (all rounds)
- planning-decisions.md (previous version, if exists)

## Writes

planning-decisions.md (create or update)

## Purpose

Deliberation produces 500+ lines of expert discussion. Critical decisions get buried. This agent extracts and structures all resolved decisions so:

- Build Order Writer can write specific acceptance criteria
- Code Selector can identify relevant sections per piece
- Implement agents receive only what they need
- Design refinements (DS-XXX) are available for UI pieces

## Output Format

```markdown
# Planning Decisions

Synthesized: [timestamp]
Last Updated: [timestamp]
Source: planning-deliberation.md (Rounds 1-N)
User Responses: [count]

---

## Architecture Decisions

### AD-001: [Decision Name]

**Decision:** [What was decided]
**Resolved:** Round N
**Confirmed:** User Response [X] (if applicable)
**Rationale:** [Why this choice]
**Implementation:**
- [Specific detail 1]
- [Specific detail 2]

---

## Integration Specifications

### IS-001: [Service/Endpoint Name]

**Endpoint:** [Method] [URL]
**Request Format:**
```json
{ "field": "type" }
```
**Response Format:**
```json
{ "field": "type" }
```
**Timeout:** [duration]
**Retry Logic:** [pattern]
**Error Handling:** [what to do on failure]

---

## Data Model Additions

| ID | Field | Table | Type | Purpose | Added |
|----|-------|-------|------|---------|-------|
| DM-001 | [field] | [table] | [type] | [why] | Round N |

---

## Cost Controls

| ID | Control | Limit | Enforcement | Rationale |
|----|---------|-------|-------------|-----------|
| CC-001 | [what] | [number] | [how] | [why] |

---

## Error Handling Patterns

### EH-001: [Scenario Name]

**Trigger:** [What causes this error]
**Detection:** [How system knows]
**Response:** [What system does]
**User Impact:** [What user sees]
**Logging:** [What gets logged]

---

## Business Rules

### BR-001: [Rule Name]

**Rule:** [Statement of the rule]
**Source:** Round N, [Expert]
**Edge Cases:**
- [case 1]: [handling]
- [case 2]: [handling]
**Enforcement:** [Where in code]

---

## Design Standards

### DS-001: [Refinement Name]

**Baseline:** [What design-system.md says]
**Refinement:** [Project-specific override]
**Rationale:** [Why baseline doesn't fit]
**Applies To:** [Which pieces/pages]
**Implementation:**
- [Specific CSS/component change]

---

## Deferred to Production

| ID | Issue | Why Deferred | Monitoring | Revisit Trigger |
|----|-------|--------------|------------|-----------------|
| DP-001 | [issue] | [reason] | [how] | [when] |

---

## Quick Reference

### Limits
- [Limit 1]: [value]
- [Limit 2]: [value]

### Key Patterns
- [Pattern 1]: [description]
- [Pattern 2]: [description]

### Design Overrides
- DS-001: [one-line summary]
- DS-002: [one-line summary]
```

## Extraction Rules

| Pattern in Deliberation | Extract To | ID Prefix |
|------------------------|------------|-----------|
| "Recommendation: Option X" | Architecture Decisions | AD- |
| "Endpoint:" / "POST /api/" | Integration Specifications | IS- |
| "Add field X to table Y" | Data Model Additions | DM- |
| "Max N" / "Limit:" / "Budget:" | Cost Controls | CC- |
| "If X happens, then Y" / "Error:" | Error Handling Patterns | EH- |
| "Must" / "Should" / "Always" (business) | Business Rules | BR- |
| "DS-XXX:" / Designer proposals | Design Standards | DS- |
| "Learn in production" / "Defer" | Deferred to Production | DP- |

## DS-XXX Extraction

Designer proposes DS-XXX during deliberation. Extract these to the Design Standards section:

**From deliberation:**
```markdown
### Designer

DS-XXX PROPOSALS:
- DS-001: Dashboard Card Density
  - Baseline: Card padding p-4, gap-6 between cards
  - Proposed: Card padding p-3, gap-4 between cards
  - Rationale: Dashboard shows 12+ metrics, tighter spacing prevents scroll
```

**To planning-decisions.md:**
```markdown
### DS-001: Dashboard Card Density

**Baseline:** Card padding p-4, gap-6 between cards
**Refinement:** Card padding p-3, gap-4 between cards
**Rationale:** Dashboard shows 12+ metrics, tighter spacing prevents scroll
**Applies To:** Dashboard page, metrics cards
**Implementation:**
- Cards: className="p-3" (not p-4)
- Grid: className="gap-4" (not gap-6)
```

## Quality Rules

1. **Every decision must have implementation detail** — not "use webhooks" but specific patterns

2. **Every limit must have enforcement mechanism** — how it's actually enforced in code

3. **Every integration must have request/response format** — actual JSON structure

4. **Every error scenario must have response action** — what system does

5. **Every DS-XXX must have implementation** — specific classes/values to use

6. **IDs must be stable across updates** — AD-001 stays AD-001

7. **Track provenance** — which round, which expert, which user response

## Update Behavior

| Situation | Action |
|-----------|--------|
| New decision | Add with new ID |
| Decision refined | Update existing, note "Updated: Round N" |
| User confirms | Add "Confirmed: User Response X" |
| User overrides | Update content, note "Override: User Response X" |
| Decision invalidated | Remove or move to Deferred |

## Statistics Output

After synthesis, output summary:

```markdown
### New Decisions (this cycle)
- AD-003: [name]
- DS-001: [name]
- DS-002: [name]

### Updated Decisions
- AD-001: [what changed]

### Confirmed by User
- AD-002: Confirmed
- DS-001: Confirmed

### Statistics
- Total decisions: N
- Architecture: N
- Integrations: N
- Data model: N
- Cost controls: N
- Error handling: N
- Business rules: N
- Design standards: N
- Deferred: N
```
