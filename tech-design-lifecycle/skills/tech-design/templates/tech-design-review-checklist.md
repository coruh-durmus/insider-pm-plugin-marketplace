# Tech Design Review Checklist

Quality criteria for generated tech designs. Used by `/tech-design` for self-validation and by humans for review.

---

## Machine-Readable Quality Metrics

| Metric | Target | How to Measure |
|--------|--------|----------------|
| Section completeness | >= 80% | % of mandatory sections with substantive content (not placeholders or "TBD") |
| Alternative count | >= 2 | Number of distinct alternatives in "Alternatives Considered" (Standard/Full templates) |
| Edge case count | >= 5 | Number of edge cases in "Edge Cases & Failure Modes" (>= 3 for Lightweight) |
| Edge case diversity | >= 3 categories | Edge cases must span at least 3 of: data consistency, error handling, concurrency, security, backward compatibility, operational, integration |
| Grounding score | >= 60% | % of component/file references that map to actual repo/file names vs. generic descriptions |
| Open question coverage | 100% | Every section with insufficient context has a corresponding open question |
| Security coverage | present | Security Considerations section addresses auth, sensitive data, and attack surface (or states N/A with rationale) |
| Resilience coverage | present | Failure Modes & Recovery section lists dependencies with fallback behavior (or states N/A with rationale) |
| Capacity coverage | present | Capacity & Performance section includes at least one quantified estimate (or states N/A with rationale) |

---

## Completeness Checks

- [ ] All mandatory template sections are filled (no empty sections)
- [ ] Problem statement is synthesized from Jira, not copy-pasted
- [ ] Current architecture references actual code (file paths, function names, service names)
- [ ] Proposed solution is concrete enough to start implementation
- [ ] Alternatives are meaningfully different (not parameter variations)
- [ ] Edge cases include specific scenarios, not generic categories
- [ ] Testing strategy names specific test scenarios
- [ ] Rollback strategy is concrete (not "revert the PR")
- [ ] Security section addresses auth, sensitive data, and attack surface (or explicitly states N/A)
- [ ] Failure Modes section lists each dependency with unavailability behavior (or explicitly states N/A)
- [ ] Capacity section includes at least one quantified volume/resource estimate (or explicitly states N/A)
- [ ] Open questions are genuine unknowns with assigned owners

## Quality Checks

- [ ] Referenced files and functions exist in the codebase
- [ ] Proposed solution follows existing architectural patterns
- [ ] All Jira acceptance criteria are addressed in the tech design
- [ ] No hallucinated service names, endpoints, or configurations

---

## Section Requirement Map

Maps ticket characteristics to mandatory sections:

| Ticket Characteristic | Required Sections |
|----------------------|-------------------|
| Any bug ticket | Root Cause, Fix Approach, Regression Risk |
| Touches auth/IAM | Security Considerations (full detail required) |
| Modifies APIs | API Contracts, Backward Compatibility |
| Adds endpoints | Security Considerations (auth middleware coverage) |
| Async processing | Failure Modes & Recovery (async failure visibility, retry policy) |
| High-traffic path | Capacity & Performance (quantified volume + resource profile) |
| New downstream dependency | Failure Modes & Recovery (unavailability handling) |
| Creates new service | Full template, all sections |
| Data model changes | Data Model, Migration Plan |
| Cross-service changes | Sequence of Operations, Integration edge cases |
| Label: `data-migration` | Migration Plan, Rollback Strategy |
| Epic-linked | Epic context in Problem Statement |
| Story points >= 8 | Implementation Plan with phases |
