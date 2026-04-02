# Incident Pattern Bank

Derived from analysis of **702 post-incident meetings** (170 with full content), 2016-2025.
Each pattern maps a proven failure mode to concrete design checks.

---

## System Risk Profiles

Historical incident density by system. When a design touches a high-incident system, elevate all related checks.

| System | PIMs | Trend | Top Failure Mode | Keywords |
|---|---|---|---|---|
| API/Panel/InOne | 82 | Worsening | DB query bottleneck, caching miss | panel, inone, gachapon, skywalker, pandora, gateway |
| UCD/Atrium | 62 | High, stable | Capacity, hot spots, VPC exhaustion | ucd, atrium, profile, upsert, segment, attribute, vertical, horizontal, hook |
| Email | 53 | Improving | Provider failure, template corruption | email, pony express, sendgrid, newsletter, honeybadger |
| App Push/Mobile | 43 | Sharp increase | Queue starvation, goroutine leak | app push, mobile push, fcm, apns, mobilesuite, asgard, aconsumer |
| Journey/Architect | 30 | Stable | Delta queue, listener failures | journey, architect, rosie, listener, canvas, honey-badger |
| Web Push | 30 | Stable | Lambda timeout, queue backup | web push, alfred, notifier, push token, lambda |
| Analytics/Reporting | 28 | Improving | Query load, data mismatch | enigma, stats, analytics, valorem, attribution |
| Onsite/Templates | 24 | Volatile | Partner site breakage, CSS corruption | onsite, template, action builder, skeleton, ins.js |
| Segmentation | 22 | Stable | Data inconsistency, upload failures | segment, contech, supra, audience, upload |
| Infrastructure | 21 | Improving | Manual change, config drift | redis, valkey, eks, rds, cloudflare, terraform |
| Recommendation | 18 | Stable | ETL failure, stale data | recommender, eureka, pcd, product catalog, chef |
| WhatsApp | 13 | New surface | Provider API, rate limits | whatsapp, wa, mindbehind, meta api |
| SMS | 11 | Stable | Regulatory, provider compliance | sms, twilio, gonzales, mercury, sender id |
| Queue/Consumer | 10 | Recurring | Backpressure, scaling, stuck consumers | queue, asynq, sqs, nsq, kafka, nats, consumer |
| Database | 6 | Stable | Connection exhaustion, query bottleneck | rds, mysql, dynamodb, clickhouse, database |
| Redis/Cache | 4 | Low but catastrophic | Global patch, cluster failure | redis, valkey, cache, elasticache |

---

## The 10 Incident Patterns

### Pattern 1: Dependency Cascade

**Root cause**: External service becomes unavailable. No fallback exists. Caller blocks, queues back up, upstream callers cascade.

**Frequency**: 25+ PIMs, every year since 2018.

**Key incidents**:
- SIM-425: Lambda moved regions, HTTP timeouts to downstream → 1 billion web pushes unsent. All partners. 12-13h to rewrite in Go.
- SIM-684: UCD horizontal timeout → 30 million conversion pushes stuck. 3h55m before noticed.
- SIM-733: NSQ aconsumer stuck (missing scaling policy, CPU settings) → 5.5h of delayed app pushes.
- SIM-289: RDS endpoint changed, dependent APIs not restarted → 12h of data inaccessibility. Partner discovered it.
- SIM-538: Partner exhausted all VPC private IPs → UCD horizontal down 5.5h. Deployments blocked.

**What to check in the design**:
1. Does the design have a Failure Modes & Recovery table?
2. For each external dependency: is "What if unavailable?" answered with a SPECIFIC action (not "we'll handle it")?
3. Is there a circuit breaker or timeout defined?
4. For async flows: what happens to in-flight messages when a dependency is down?
5. Does recovery behavior account for the backlog accumulated during downtime?

**Red flags**:
- Failure Modes table is empty, missing, or has only "N/A"
- Design mentions calling an external service but has no error handling section
- Phrases: "assume it's always available", "internal service so it's reliable", "will add error handling later"

**Minimum mitigation**:
- Failure Modes table with one row per external dependency
- Each row must have a specific detection mechanism and recovery action
- Async flows must define what happens to queued work during outage

---

### Pattern 2: Noisy Neighbor

**Root cause**: Single partner sends abnormal volume. Shared infrastructure has no per-tenant isolation. All partners affected.

**Frequency**: 12+ PIMs, concentrated in 2023-2025.

**Key incidents**:
- SIM-538/543: Partner `kbsecmableprod` — 800K users, 30M events — exhausted all VPC private IPs. UCD horizontal down 5.5h. All products affected.
- SIM-677: Incorrect integration wrote all events to single user → DynamoDB hot spot → all event traffic throttled for 1h.
- SIM-684: `mbbank` overloaded UCD horizontal requests → conversion push queue blocked. 30M pushes stuck.
- SIM-499/526: LCW coupon campaigns crashed Pandora/InOne. Same partner, same mechanism, 6 weeks apart.
- SIM-522: salesdemo partner accumulated 130K-150K campaigns (periodic deletion broken) → RDS query queue backed up → panel slow for all.

**What to check in the design**:
1. Does the design process partner data on shared infrastructure?
2. Is there per-partner rate limiting or resource isolation?
3. What happens if one partner sends 100x normal volume?
4. Are there per-partner quotas on stored data (campaigns, events, users)?
5. Can a single partner's data size cause a hot spot in the storage layer?

**Red flags**:
- Shared Redis/DynamoDB/RDS used without per-partner key partitioning
- No mention of rate limiting for partner-facing endpoints
- Design assumes "partners send roughly equal volume"
- Batch processing without per-partner bounds

**Minimum mitigation**:
- Per-partner rate limit on ingestion endpoints
- Or: architectural isolation (separate queues, partitioned storage)
- Or: explicit statement of why tenant isolation is unnecessary for this specific change

---

### Pattern 3: Blind Failure

**Root cause**: System fails silently. No alert fires. Discovery happens hours or days later, often by a partner or customer.

**Frequency**: 20+ PIMs. Present in every era.

**Key incidents**:
- SIM-289: CRM/Report APIs down 12 hours. Partner noticed, not internal monitoring.
- SD-45121: Partner site buttons unclickable for 12-15 hours. Detected by partner.
- SIM-689: API push queue building for 2 days before detection. Service ran out of sockets.
- SIM-684: 30M pushes stuck for 3h55m. Noticed late.
- SIM-821: Goroutine leak detected 4-5 hours before action. Test environment instability delayed response.
- Contrast: SIM-975 — Cloudflare rule detected in 2 minutes via Site 24x7 → fixed in 2 minutes.

**The math**: Median time-to-fix is ~45 minutes. Median time-to-detect is 3-4x longer. Investing in detection gives 4x ROI vs faster fixes.

**What to check in the design**:
1. Does the design state HOW a failure will be detected?
2. Is there a target detection time? (Must be a number, not "quickly")
3. Are there specific alert thresholds defined (not "we'll add monitoring")?
4. For async/background processes: how is "stuck" distinguished from "slow"?
5. Who gets paged, and through what channel?

**Red flags**:
- Monitoring section says "add Datadog alerts" without specifying what metric or threshold
- No mention of monitoring for new background processes or queues
- Design relies on partner reports to detect failures
- Phrases: "we'll monitor", "alerting TBD", "add monitoring later"

**Minimum mitigation**:
- Detection SLA: "This system must detect failure within X minutes via Y mechanism"
- At least one specific alert rule per new flow (metric name, threshold, channel)
- For queues: alert on queue depth growth rate, not just absolute depth

---

### Pattern 4: Blast Radius Amplification

**Root cause**: Change intended for a limited scope affects everything. No isolation between change target and the rest of the platform.

**Frequency**: 12+ PIMs. Includes the most severe incidents in the dataset.

**Key incidents**:
- SIM-983: Security patch intended for one Redis cluster applied to ALL Redis and Valkey clusters. Every product line affected. Potential permanent data loss. AWS IAM had no guardrails.
- SIM-975: Cloudflare rule intended for one subdomain applied to entire `useinsider.com` zone. All subdomains 403. Fixed in 2 min (fast detection).
- PA-13153: Spark 3.1.1 upgrade + ETL change deployed together → all partner homepages corrupted. Campaigns with wrong characters couldn't be deactivated.
- SIM-672: `ScanIndexForward` misconfiguration → 493 partners, 6,309 campaigns, 109.8 million users. Wrong event calculations. 3-day recovery.
- MES-4377: Design system upgrade bundled with unrelated component work → multi-language dropdown corrupted.

**What to check in the design**:
1. Is this change deployed independently from other changes?
2. Is there a feature flag or phased rollout plan?
3. What is the maximum number of partners/users affected if this goes wrong?
4. Can the change be rolled back independently?
5. For infrastructure changes: is the scope explicitly bounded?

**Red flags**:
- Multiple unrelated changes in a single deployment
- No feature flag for a change that affects all partners
- Design says "deploy to all partners at once"
- Infrastructure changes via manual console operations
- Phrases: "big bang migration", "deploy everything together", "one release for all"

**Minimum mitigation**:
- Feature flag for partner-facing changes, or phased rollout (% of traffic)
- Independent deployment per change (no bundling unrelated work)
- Stated maximum blast radius: "If this fails, at most X partners are affected"

---

### Pattern 5: Queue Starvation

**Root cause**: Queue consumers fall behind producers. No backpressure, no scaling policy, no dead-letter queue. Messages accumulate until the system is overwhelmed.

**Frequency**: 10+ PIMs. Concentrated in push notification and event processing systems.

**Key incidents**:
- SIM-684: Conversion push queue backed up. 30M pushes not sent. Noticed 3h55m late.
- SIM-733: NSQ aconsumer stuck — missing scaling policy, CPU settings, concurrent handler config. 5.5h delay.
- SIM-689: API push service ran out of sockets. No connection limits, no queue system. 2-day recovery.
- SIM-470: Push queue cleanup cascaded to recurring pushes. Related to SIM-457 (prior incident).
- SIM-926: Redis queue messages skipped after DB restart. Go client transport stuck for 2 days.

**What to check in the design**:
1. If the design uses queues: what happens when consumers are slower than producers?
2. Is there a dead-letter queue for messages that fail repeatedly?
3. Is there a max queue depth alert?
4. What is the consumer scaling policy (auto-scale, fixed, manual)?
5. What happens to in-flight messages if the consumer crashes?
6. Is there a separate retry queue (not re-enqueue to main queue)?

**Red flags**:
- Queue introduced without consumer scaling policy
- No dead-letter queue defined
- No mention of message TTL or expiry
- Consumer has no concurrency limit (unbounded goroutines/connections)
- Phrases: "messages will be processed eventually", "queue handles backpressure"

**Minimum mitigation**:
- Max queue depth alert (not just on consumer errors — on depth growth rate)
- Dead-letter queue for messages that fail N times
- Consumer concurrency limit defined
- For critical flows: separate retry queue

---

### Pattern 6: Configuration Drift

**Root cause**: Infrastructure changed manually via cloud console. No peer review, no scope guard, no automation. Human error at the console causes maximum damage.

**Frequency**: 5+ PIMs. Low frequency but highest severity class.

**Key incidents**:
- SIM-983: Operator applied Redis security patch to ALL clusters instead of one. AWS IAM had no guardrails preventing multi-cluster modifications. Widest blast radius in dataset.
- SIM-975: Cloudflare rule applied to entire `useinsider.com` zone instead of specific subdomain. All subdomains returned 403.
- SIM-289: RDS endpoints updated in parameter store, dependent APIs not restarted. 12h outage.
- SIM-538: Docker Hub rate limits blocked new deployments. No ECR mirror existed.

**What to check in the design**:
1. Does the design involve infrastructure changes (new resources, config changes, provider settings)?
2. Are these changes codified (Terraform, CloudFormation, Helm) or manual?
3. Is there a peer review requirement for infrastructure changes?
4. Are there scope guards (IAM policies, blast radius limits) preventing wider-than-intended changes?

**Red flags**:
- Design says "update the settings in AWS console" or "change the Cloudflare rule"
- Infrastructure changes described as manual steps in a runbook
- No mention of IaC for new infrastructure resources
- Phrases: "manually configure", "update via console", "run this command in production"

**Minimum mitigation**:
- All infrastructure changes via IaC (Terraform/Helm/CloudFormation) with PR review
- Or: if manual change is unavoidable, dual-engineer verification + explicit scope bounds

---

### Pattern 7: Untested Change Path

**Root cause**: Code was modified but the new path was never tested under production conditions. Environments differ (K8s vs Jenkins vs Docker). Post-review changes skip retesting.

**Frequency**: 15+ PIMs. Persistent across all eras.

**Key incidents**:
- SD-45121: Code changed after UAT, change never retested → 12-15h of partner disruption.
- SD-49973: Post-code-review changes deployed without retesting.
- PA-13153: Spark 3.1.1 tested in staging, but staging didn't test UTF-8 edge cases → all partner homepages corrupted.
- SIM-672: `ScanIndexForward` ordering differed between v1 and v2. Missed in testing. 493 partners, 109.8M users, 3-day recovery.
- SD-67432: Environment inconsistency between K8s and Jenkins — automation passed in one, failed in other.

**What to check in the design**:
1. Is there a testing strategy that covers the specific code paths being changed?
2. Does testing happen in the same environment type as production?
3. Is there a requirement to retest if code changes after review?
4. For migrations or version changes: are both old and new behavior tested?
5. Are cross-product impacts tested (change in product A affects product B)?

**Red flags**:
- Testing strategy says "unit tests" only for a change with cross-service impact
- No mention of testing in a production-like environment
- Design modifies behavior at version boundaries (v1→v2) without migration testing
- Phrases: "tested locally", "works on my machine", "QA will catch it"

**Minimum mitigation**:
- Testing in production-equivalent environment for cross-service changes
- Explicit statement: "if code changes after review, full test suite re-runs"
- For v1→v2 transitions: test both old and new paths, including ordering/boundary differences

---

### Pattern 8: Cross-Team Blind Spot

**Root cause**: Team A changes a shared service. Team B depends on it but isn't informed. The change breaks Team B's flows.

**Frequency**: 10+ PIMs. Persistent.

**Key incidents**:
- SIM-289: RDS endpoints changed by infra team. CRM/Report APIs (different team) not restarted. 12h outage.
- SD-45471: Location service migration code removed. Mobile team still used old path. Mobile location broke.
- SIM-733: New aconsumer integration had no cross-team handshake process. Missing scaling config.
- MES-4377: Design system upgrade broke downstream consumers (multi-language dropdown). Revert without team notification.
- SIM-538: Docker Hub rate limits affected all teams' deployments. No prior coordination.

**What to check in the design**:
1. Does the design change a shared service or a service consumed by other teams?
2. Are all consuming teams listed?
3. Have consuming teams been notified/consulted?
4. If deprecating an interface: have all consumers migrated?
5. Is there a communication plan for deployment (Slack notification, release notes)?

**Red flags**:
- Design modifies a service in the org dependency map that has downstream consumers, without listing them
- Design removes or changes an API without verifying all callers
- No cross-team section in the design for a change to shared infrastructure
- Phrases: "internal service, no one else uses it" (verify this claim)

**Minimum mitigation**:
- List all downstream consumers from the dependency map
- For each: confirmation that they've been notified (who, when)
- For deprecations: verification that all consumers have migrated before removal

---

### Pattern 9: Missing Recovery Path

**Root cause**: System fails. No mechanism to recover or replay in-flight work. Data or messages are permanently lost.

**Frequency**: 8+ PIMs. Concentrated in messaging/push delivery.

**Key incidents**:
- SIM-425: 1 billion web pushes unsent. Cannot be replayed — delivery window expired.
- SIM-672: Wrong event calculations for 109.8M users. 3-day recovery to recalculate.
- SIM-684: 30M conversion pushes stuck. Recovery path unclear.
- SIM-821: App push delivery delayed. Delayed pushes could not be resent.
- SIM-983: Redis/Valkey outage. Mobile click/view metrics: potential permanent data loss. Coupon campaigns: 11 reverted.

**What to check in the design**:
1. If this system fails mid-operation, is work-in-progress recoverable?
2. Are messages/events idempotent (can be safely replayed)?
3. Is there a dead-letter mechanism for failed processing?
4. For data mutations: is there a rollback or compensation mechanism?
5. What is the acceptable data loss window? Is it documented?

**Red flags**:
- Design processes time-sensitive messages (push notifications, real-time events) without replay capability
- No mention of idempotency for message processing
- Data mutations without transaction boundaries or rollback plan
- Phrases: "fire and forget", "best effort delivery", "we accept some loss"

**Minimum mitigation**:
- For critical flows: define maximum acceptable data loss window
- For message processing: idempotency mechanism (dedup key, processed-set)
- For data mutations: rollback procedure or compensation logic

---

### Pattern 10: Recurrence Loop

**Root cause**: An incident happens. Action items address symptoms (add alert, restart pod). Root cause is structural. Same incident recurs weeks later.

**Frequency**: 8+ PIMs with explicit recurrence.

**Key incidents**:
- SIM-665 → SIM-679: Same Contour pod termination, 22 days apart. Action items from first were not completed.
- SIM-499 → SIM-526: LCW coupon campaigns crashed Pandora. Same partner, same mechanism, 6 weeks apart.
- SIM-484: "Same incident repeated from 15 days before." Segmentation common service, no failure strategy.
- SIM-538 → SIM-543: UCD horizontal failures on consecutive nights.
- AWS EKS LB: "Same problem experienced before." LB reset to defaults.

**What to check in the design**:
1. Has this system experienced incidents before? (Search PIMs)
2. If yes: does the design address the root cause from those incidents?
3. Are there known recurring failure modes in this area?
4. Does the design add structural prevention (architecture change) or just operational mitigation (alert, restart)?

**Red flags**:
- Design touches a system with known recurring incidents but doesn't reference them
- Mitigation section only contains operational fixes ("add monitoring", "restart on failure")
- No architectural change despite known structural weakness

**Minimum mitigation**:
- If the system has prior incidents: reference them and state how this design prevents recurrence
- At least one structural mitigation (not just "add alert")

---

## Cascade Risk Zones

High-risk dependency chains from the org architecture where a single failure cascades across multiple teams.

### Zone 1: UCD → Everyone
UCD (Atrium) is consumed by Journey Builder, Scalability (OnSite triggers), Valorem (attribution), Mobile, Contech (segments), Kraken (upsert). UCD downtime cascades to ALL teams. 62 PIMs.

### Zone 2: Redis → Everyone
Redis is used for caching, rate limiting, session state, observer tracking, and queue backing across all services. A Redis failure is a platform failure. SIM-983 proved this.

### Zone 3: Journey Builder → All Channels
Rosie dispatches to Email, SMS, App Push, Web Push, WhatsApp, In-App, Facebook, Google, Call-an-API, Web Channel. A Journey Builder failure blocks all channel delivery.

### Zone 4: Gachapon → Everyone
Gachapon (partner management, settings) is read by all teams. A Gachapon outage or slow query affects every service that checks partner config.

### Zone 5: Queue Layer → Consumers
NSQ/Asynq/SQS/Kafka/NATS feed all consumer services. Queue infrastructure failure or backpressure cascades to data processing, push delivery, and DWH sync.

---

## Detection Time Benchmarks

From PIM analysis, the target detection times that would have prevented escalation:

| System | Recommended Detection SLA | Based on |
|---|---|---|
| Partner-facing API | < 2 minutes | SIM-975: 2min detect → 2min fix |
| Push delivery pipeline | < 15 minutes | SIM-684: 3h55m late detection escalated to 30M lost |
| Queue depth / consumer health | < 5 minutes | SIM-733: stuck consumer, 5.5h delay |
| UCD / data platform | < 5 minutes | SIM-538: 5.5h undetected exhausted all recovery options |
| Background/async jobs | < 30 minutes | SIM-821: goroutine leak detected 4-5h before action |
| Infrastructure (Redis, DB) | < 2 minutes | SIM-983: fastest detection = fastest recovery |
