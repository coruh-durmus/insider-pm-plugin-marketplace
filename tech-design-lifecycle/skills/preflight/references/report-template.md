# Report Template

Use this exact structure for the preflight report output.

```
# Preflight Report: <ticket-key or filename>

**Team**: <detected team>
**Systems touched**: <affected systems from Step 1>
**Cascade zones**: <zones hit, or "None">
**Related incidents found**: <count from Step 3>

**Review readiness**: <PASS count>/17 categories covered
**Incident risk**: <CRITICAL count> critical, <HIGH count> high, <MEDIUM count> medium, <LOW count> low

---

## Critical — Incident-Backed Gaps

### [CRITICAL] <Category> — <one-line gap>

**This has caused production incidents:**
→ <PIM>: <what happened, impact, recovery time>
→ <PIM>: <what happened>

**What's missing in your design:**
<specific gap>

**Add to your design:**
<concrete text or table the author can add to close the gap>

---

## High Priority Gaps

### [HIGH] <Category> — <one-line gap>

<Why: team fingerprint data + incident pattern evidence>
Reviewers will ask:
  → <question from question bank>
  → <question from question bank>
<If incident pattern: "Known failure mode: <pattern name>. See: <PIM references>">
<If existing solution found: "Found existing: <what was found>">

---

## Medium Priority Gaps

### [MEDIUM] <Category> — <one-line gap>

Reviewers will ask:
  → <question>

---

## Low Priority (Shallow Coverage)

### [LOW] <Category> — <what's shallow>

Consider adding: <specific improvement>

---

## Existing Solutions Found

<Codebase patterns or prior designs found in Step 3 that the design doesn't reference>
<If none: "No unreferenced prior art found.">

## Incident History for Your Systems

<PIMs found via search, grouped by system>
<For each: title, root cause, relevance to this design>
<If none: "No prior incidents found for the affected systems.">

## Cascade Analysis

<Dependency chains from Step 4>
<Unprotected cascade paths flagged>
<Recovery scenario summary>

## Jira Cross-Reference

<Unaddressed acceptance criteria from Step 4>
<Scope mismatches>
<If all addressed: "All acceptance criteria covered.">

---

## Categories Covered (<PASS count>)

<Bulleted list of PASS categories with one-line confirmation>
```
