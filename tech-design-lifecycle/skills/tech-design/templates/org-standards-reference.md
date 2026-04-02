# Organizational Standards Reference

Standards that AI-generated tech designs must conform to. Update this file as team standards evolve.

---

## Deployment Standards

- **Feature flags**: Required for any partner-facing change that can be gradually rolled out
- **Rollback plan**: Every tech design must include a concrete rollback strategy (not "revert the PR")
- **Database migrations**: Must be backward-compatible -- no breaking schema changes in a single deploy
- **Independent deploys**: Changes should be deployable independently; do not bundle unrelated work

## Multi-Tenancy & Partner Safety

- Per-partner rate limiting on ingestion endpoints
- Shared infrastructure must have tenant isolation or explicit justification for why it's unnecessary
- Design must answer: "What happens if one partner sends 100x normal volume?"
- No single partner's data or load should degrade service for others

## Resilience & Recovery

- Every external dependency must have a Failure Modes & Recovery entry: what if unavailable, how detected, how recovered
- Async flows must define what happens to in-flight work during outages
- Circuit breakers or timeouts for all external service calls
- Dead-letter queues for messages that fail repeatedly
- Retry logic must distinguish retryable from non-retryable errors

## Observability

- Structured logging with request IDs
- Metrics exposed for monitoring (counters, histograms) on all services
- Health check endpoints on all services
- Sentry error reporting for unexpected failures
- Detection SLA: every new flow must state how failure is detected and within what timeframe
- Queue depth alerts on growth rate, not just absolute depth

## Security

- No secrets in code -- use environment variables or a secrets manager
- All endpoints require authentication unless explicitly public
- PII must be logged with redaction
- Input validation at service boundaries
- Parameterized queries for all database access

## Infrastructure Changes

- All infrastructure changes via IaC (Terraform/Helm/CloudFormation) with PR review
- If manual change is unavoidable: dual-engineer verification + explicit scope bounds
- Scope guards (IAM policies, blast radius limits) to prevent wider-than-intended changes

---

## How This File Is Used

The `/tech-design` skill references these standards when:
1. Proposing solutions (align with approved patterns)
2. Evaluating alternatives (flag deviations from standards)
3. Identifying risks (standard violations = risks)
4. Self-validating (check proposed architecture against these patterns)
