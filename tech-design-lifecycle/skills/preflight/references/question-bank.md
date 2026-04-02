# Preflight Question Bank

Derived from analysis of 4,736 tech design review comments across 728 designs, 12 teams, 2018-2025.
Each category lists the questions reviewers most commonly ask. Used by `/preflight` to check tech designs.

---

## Team Fingerprints

Per-team severity elevation — if a category is a team's top concern, elevate missing coverage to HIGH.

| Team | Elevated Categories |
|---|---|
| Bytemasters | Backward Compatibility & Migration, Dependencies & Integration, Performance & Scalability |
| Dataforce Technical Desgins | Architecture & Design, API Design & Contracts, Cost & Resource |
| Scalability | Performance & Scalability, Security & Auth, Architecture & Design, Backward Compatibility & Migration |
| Pirates | Performance & Scalability, Cost & Resource, Error Handling & Resilience |
| Qmechanics | Business Logic & Edge Cases, Dependencies & Integration, Security & Auth |
| Minerva | API Design & Contracts, Architecture & Design, Monitoring & Observability, Dependencies & Integration |
| Vikings | Security & Auth (regulatory), Architecture & Design |
| Valorem | Existing Solution Awareness, Architecture & Design |
| Kraken | API Design & Contracts, Backward Compatibility & Migration, Performance & Scalability |
| Contech | Architecture & Design, Multi-tenancy & Partner Impact |
| Mobile | Multi-tenancy & Partner Impact, Business Logic & Edge Cases |
| Atrium | Performance & Scalability, Testing & Quality |

---

## Category Questions

### Architecture & Design (47 designs, 12 teams)

Section check: "Architecture → Proposed Changes" must exist with substantive content.

Depth questions:
- Is the service boundary explicitly justified? Why does this logic live here and not in another service?
- Does this create a second source of truth for any data?
- Are we building something that will be thrown away after a planned structural refactor?
- Is the proposed design consistent with existing architectural patterns in the codebase?
- If introducing a new service or module: why can't this be an extension of an existing one?

### Performance & Scalability (43 designs, 10 teams)

Section check: "Capacity & Performance" must exist with at least one quantified estimate.

Depth questions:
- What is the expected QPS/volume at peak? (A number is required, not "should handle it")
- What is the Redis/DB memory profile at 10x current load?
- Are there hot loops hitting the database without a cache layer?
- What is the payload size or item size, and does it fit within system limits (DynamoDB 400KB, SQS 256KB)?
- For client-side changes: what is the DOM/CPU impact on partner pages at scale?

### Business Logic & Edge Cases (38 designs, 11 teams)

Section check: "Edge Cases & Failure Modes" must have >= 3 entries spanning >= 3 categories.

Depth questions:
- What happens with concurrent edits or simultaneous operations?
- What happens with empty/zero/null states?
- What happens at boundary values (max items, min values, exactly-at-limit)?
- What happens if the user abandons mid-flow?
- Are there partner-specific business rules that differ from the general case?

### Scope & Requirements (34 designs, 11 teams)

Section check: "Scope & Constraints" must define both In Scope and Out of Scope.

Depth questions:
- Does the design's scope match the Jira ticket's acceptance criteria?
- Is anything described in the design not actually requested in the requirements?
- Are there ambiguous requirements that the design has silently interpreted one way?
- Is the phasing clear — what ships in v1 vs. later iterations?

### Dependencies & Integration (34 designs, 9 teams)

Section check: "Outsourced Services" or dependency mentions in "Open Questions" or "Architecture".

Depth questions:
- Are all cross-team dependencies listed with confirmation status (agreed / not yet / unknown)?
- For each dependency: is the other team's work on their roadmap and timeline-aligned?
- What is the fallback if a dependency is not ready in time?
- Are API contracts between services defined (who owns the contract)?
- Are third-party service SLAs and rate limits accounted for?

### Backward Compatibility & Migration (31 designs, 10 teams)

Section check: "Migration & Rollback" must exist with concrete steps (not "revert the PR").

Depth questions:
- What happens to existing live data, campaigns, or journeys after this change?
- Is there a dual-write or parallel-running period?
- Can the migration be rolled back, and what is the rollback procedure?
- Are existing API consumers or clients affected? If so, what is the deprecation timeline?
- Is a data migration command or script needed? What is the expected runtime?

### API Design & Contracts (31 designs, 9 teams)

Section check: "Component Breakdown" must mention API/interface changes if the design modifies or adds endpoints.

Depth questions:
- Are request/response examples provided for each new or modified endpoint?
- Are HTTP methods correct (POST/PUT for state changes, not GET)?
- Are response codes and error formats defined?
- Is API versioning addressed if breaking changes are introduced?
- Is the endpoint naming consistent with existing API conventions?

### Data Consistency & Integrity (30 designs, 11 teams)

Section check: Edge cases table must include at least one "data consistency" category entry.

Depth questions:
- What is the primary key / uniqueness guarantee for new data?
- Are there race conditions between concurrent writes?
- If using eventual consistency: what is the inconsistency window and is it acceptable?
- Is deduplication handled for messages/events that may be delivered more than once?

### Multi-tenancy & Partner Impact (29 designs, 9 teams)

Content check: design must acknowledge partner-level behavior if the change affects partner-facing features.

Depth questions:
- Does this work correctly for partners with non-standard configurations?
- Is there partner-specific rate limiting or resource isolation?
- Can one partner's load or data affect another partner's experience?
- Are there partners that will need different behavior (e.g., regional compliance)?

### Security & Auth (27 designs, 10 teams)

Section check: "Security Considerations" must address auth, sensitive data, and attack surface.

Depth questions:
- Do new endpoints have the same auth middleware as existing ones?
- Is any partner/user ID exposed in public URL paths or query parameters?
- Are redirect URLs whitelisted, or passed through from client input?
- Does client-side code handle tokens safely (no localStorage for secrets, CSP compliance)?
- Does the design comply with data residency requirements for regulated markets?
- Are internal-only endpoints still protected with authentication?

### Error Handling & Resilience (26 designs, 9 teams)

Section check: "Failure Modes & Recovery" must list dependencies with unavailability behavior.

Depth questions:
- For each downstream dependency: what happens if it is unavailable?
- How are async job failures detected and surfaced? (Not "we'll monitor" — what specific signal?)
- Does the retry logic distinguish retryable from non-retryable errors?
- What is the max retry count and backoff strategy?
- What does recovery look like after an outage (not just pause)?

### Cost & Resource (25 designs, 9 teams)

Section check: "Benchmark and Cost Estimation" should exist for infrastructure-heavy changes.

Depth questions:
- What is the estimated infrastructure cost (Lambda executions, Redis memory, DB storage)?
- How does cost scale with partner growth?
- Is there a cheaper alternative that was considered?

### Monitoring & Observability (23 designs, 8 teams)

Section check: "Monitoring and Alarms" must exist with specific metrics or thresholds.

Depth questions:
- What new metrics are exposed and on which dashboards?
- What alert thresholds trigger, and who gets paged?
- For async or ML workloads: how do you detect silent degradation (stale models, drifting accuracy)?
- Can this be debugged in production without redeploying?

### Testing & Quality (20 designs, 8 teams)

Section check: "Testing Strategy" must name specific test scenarios (not generic "unit tests").

Depth questions:
- Are integration tests planned for cross-service interactions?
- Is QA automation planned alongside development, or as a separate phase?
- For data migrations: how is correctness validated post-migration?

### Deployment & Rollout (19 designs, 9 teams)

Section check: "Migration & Rollback" must include deployment mechanics (not just data migration).

Depth questions:
- Is a feature flag used for gradual rollout?
- What is the canary/rollout plan (% of traffic, duration per stage)?
- Can this be deployed independently of the frontend/backend counterpart?
- Is the deployment order specified if multiple services change simultaneously?

### Effort & Timeline (15 designs, 5 teams)

Content check: if the design describes phases or milestones, are they realistic given the scope?

Depth questions:
- Does the effort estimate account for testing and QA, not just development?
- Are there risks that could significantly expand the timeline (dependency delays, unknowns)?

### Existing Solution Awareness (15 designs, 4 teams)

Active check: search the codebase and Confluence for similar patterns.

Depth questions:
- [Automated] Does a similar service, library, or pattern already exist in the codebase?
- [Automated] Has a prior tech design addressed this domain?
- Was the relevant platform team consulted before designing from scratch?
