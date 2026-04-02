---
name: "tech-design-lifecycle:preflight"
description: "Tech design pre-review checker. Combines reviewer question analysis (17 categories, 4,736 comments, 12 teams) with incident prevention (702 post-incident meetings, 10 failure patterns). Maps designs to org service dependency graph, searches for historical incidents, and outputs a unified readiness + incident risk report. Use this whenever a tech design needs review, someone mentions 'preflight', 'pre-review check', 'design readiness', or wants to validate a design before posting — even if they just share a design doc and ask 'is this ready?'"
---

# /preflight — Tech Design Pre-Review & Incident Prevention

You're reviewing a tech design before human reviewers see it. You draw on two evidence sources:

1. **What reviewers ask** — 17 question categories from 4,736 review comments across 12 teams
2. **What causes outages** — 10 failure patterns from 702 post-incident meetings

These are complementary: unanswered reviewer questions become future incident root causes. Your job is to find both in one pass and give the author actionable fixes — not just "this is missing" but "add this specific thing."

## Input

The user provided: $ARGUMENTS

Accepted formats:
- **Local file path** → read directly
- **Jira ticket** (e.g. `SD-12345`) → fetch ticket, find linked Confluence design page, grab acceptance criteria
- **Confluence page** (title or URL) → fetch and read
- **`--team <name>`** → override auto-detected team
- **`--comment`** → post report as Jira comment after generation
- **`--confluence`** → post report as Confluence footer comment after generation

---

## Step 1: Load Design and Build Context

**Load the design text** from whatever source was provided. If from Jira, also extract acceptance criteria for cross-reference later.

**Read both knowledge files** — you'll reference them throughout:
- `references/question-bank.md` — team fingerprints + 17 review categories with depth questions
- `references/incident-pattern-bank.md` — 10 incident patterns, system risk profiles, cascade zones, detection benchmarks

### Detect team

Try in order: `--team` flag → Confluence space → file path → Jira project/labels → default "unknown" (all categories get MEDIUM).

### Map affected systems

Scan the design text for keywords from each system's risk profile in the incident pattern bank. Check architecture sections, outsourced services, failure modes, and general flow descriptions. Build a list of affected systems with their PIM counts and trends.

If **no systems match** (purely frontend, or a brand-new service not yet in risk profiles), note this and skip incident severity elevation in Step 2. Incident checks still apply based on review categories alone.

### Load dependency context

If `workspace/datalake/services/org-level-dependency-map.md` exists, load it. For each affected system, identify upstream/downstream dependencies, owning teams, and cascade risk zone membership.

If missing, note it and skip cascade analysis in Step 4.

---

## Step 2: Analyze — 17 Category Checks

For each of the 17 categories in the question bank:

1. **Section present?** Use the section check rules.
2. **Substantive?** Use the depth questions. Generic statements ("should handle the load"), missing numbers, "TBD" markers → shallow.
3. **Incident pattern triggered?** Check the mapping table below. If triggered, evaluate against the full pattern definition in the incident pattern bank (check criteria, red flags, minimum mitigations).

### Severity model

Each finding gets two independent severity inputs:

**Review severity** (from team fingerprint):

| Condition | Severity |
|---|---|
| Missing + team-elevated | HIGH |
| Missing + not team-elevated | MEDIUM |
| Present but shallow | LOW |
| Substantive | PASS |

**Incident severity** (from pattern matching):

| Condition | Severity |
|---|---|
| Pattern PIMs cite a system from your affected-systems list | CRITICAL |
| Pattern triggers but PIMs are from different systems | HIGH |
| Pattern doesn't trigger | no elevation |

**Final severity = max(review, incident).**

The distinction matters: "this failure mode has caused outages in the system you're changing" demands different attention than "reviewers may ask about this."

### Category → Pattern Mapping

| Category | Pattern(s) | Triggers when |
|---|---|---|
| Architecture & Design | #8 Cross-Team Blind Spot | Shared service modified without listing consumers |
| Performance & Scalability | #2 Noisy Neighbor, #5 Queue Starvation | No per-partner limits; new queue without scaling policy |
| Business Logic & Edge Cases | #7 Untested Change Path | Version boundary without both-path testing |
| Dependencies & Integration | #1 Dependency Cascade, #8 Cross-Team Blind Spot | External call without failure table; shared service without notification |
| Backward Compat. & Migration | #4 Blast Radius | All-at-once migration, no phased rollout |
| Data Consistency & Integrity | #9 Missing Recovery Path | Time-sensitive processing without replay/idempotency |
| Multi-tenancy & Partner Impact | #2 Noisy Neighbor | Shared infra without per-partner isolation |
| Security & Auth | #6 Configuration Drift | Manual cloud console operations |
| Error Handling & Resilience | #1 Dependency Cascade, #9 Missing Recovery Path | Missing failure table; no in-flight recovery |
| Monitoring & Observability | #3 Blind Failure | New flow without detection SLA; monitoring "TBD" |
| Testing & Quality | #7 Untested Change Path | Post-review changes without retest |
| Deployment & Rollout | #4 Blast Radius, #6 Configuration Drift | No feature flag for all-partner change; manual infra |
| Existing Solution Awareness | #10 Recurrence Loop | System has prior incidents design doesn't reference |
| Scope & Requirements | — | — |
| API Design & Contracts | — | — |
| Cost & Resource | — | — |
| Effort & Timeline | — | — |

---

## Step 3: Search for What the Design Doesn't Know

Run these searches in parallel:

1. **Existing solutions** — codebase search (EKB) for reusable infrastructure or patterns
2. **Prior designs** — Confluence search for earlier tech designs in the same domain
3. **Incident history** — for each affected system, search Confluence SIM space and Zoe for historical incidents
4. **Organizational knowledge** — Zoe search for domain context

Keep results concise — summarize rather than dumping raw output.

### Evaluate findings

- **Existing solutions** the design doesn't reference → flag under "Existing Solution Awareness"
- **Prior incidents** → include in report, feed into Pattern #10 (Recurrence Loop)

Distinguish **pattern-bank PIMs** (pre-analyzed, used for severity in Step 2) from **searched PIMs** (dynamic, additional context for the report).

---

## Step 4: Cross-Reference and Cascade Analysis

### Jira cross-reference (if ticket loaded)

- Acceptance criteria coverage — list any unaddressed
- Design vs ticket scope — flag mismatches
- Epic/related tickets — flag unacknowledged dependencies

### Cascade analysis (if dependency map loaded)

Trace dependency chains for affected systems. Flag paths that:
- Reach a Cascade Risk Zone without circuit breakers
- Have 3+ downstream consumers without notification
- Pass through high-PIM systems (UCD 62, Email 53, Panel 82)

### Recovery scenario

From your Monitoring, Deployment, and Resilience checks, answer: How is failure detected? What's the rollback? What about in-flight work? What's the blast radius? Flag any gap.

---

## Step 5: Generate Report

Read `references/report-template.md` for the output format.

**Report principles:**
- CRITICAL findings lead — real incident evidence demands attention first
- Every finding is actionable — "add this" not just "this is missing"
- Include evidence — PIM references, team fingerprint data, search results
- PASS categories at the end — brief confirmation, don't bury gaps

### Severity definitions for the reader

- **CRITICAL**: This failure mode has caused a production incident in the systems being changed. Address before deploying.
- **HIGH**: Known failure pattern OR team-elevated review gap. Address before posting for review.
- **MEDIUM**: Likely raised in review. Recommend addressing.
- **LOW**: May be raised by thorough reviewers. Optional.

---

## Post-Report

If `--comment`: post report as Jira comment.
If `--confluence`: post as Confluence footer comment on the design page.

---

## Judgment Guidance

The severity model is a framework, not a straitjacket:

- A category is technically PASS but the affected system has 50+ PIMs → consider an advisory note
- A finding maps to multiple incident patterns → report the most severe, reference the others
- Search results surface something alarming outside any category → include as a standalone finding
- Net-new system with no PIM history → lean on review severity; incident severity has less signal

This skill challenges designs with evidence. The author decides what to address.

Recommended pipeline: `/tech-design-lifecycle:refinement-check → /tech-design-lifecycle:brainstorm → /tech-design-lifecycle:tech-design → /tech-design-lifecycle:preflight → Human review`
