---
name: "tech-design-lifecycle:tech-design"
description: "Generate a tech design from a Jira ticket. Auto-fetches context from Jira, codebase (EKB), and Confluence, generates a design document, publishes to Confluence, and links back to Jira."
---

# /tech-design -- Generate Tech Design from Jira Ticket

You are generating a tech design document. The user has provided a Jira ticket key as input: $ARGUMENTS

Follow these phases in order. Do not skip phases. Do not ask clarifying questions until the end -- gather all context first, generate the tech design, then list open questions.

---

## Phase 1: Gather Jira Context

First, determine the Atlassian cloud ID by calling `mcp__claude_ai_Atlassian__getAccessibleAtlassianResources`. Cache this cloud ID for all subsequent Atlassian calls.

### 1a. Fetch the source ticket

Call `mcp__claude_ai_Atlassian__getJiraIssue` with:
- `issueIdOrKey`: the ticket key from the user input
- `responseContentFormat`: "markdown"

Extract and note:
- Title/summary
- Full description
- Acceptance criteria (often in description or a custom field)
- Issue type (Bug, Story, Epic, Sub-task)
- Status, priority, labels
- Sprint and epic link
- Assignee (will be used as Developer in the tech design)
- All comments (read them for context, decisions, clarifications)

### 1b. Find related tickets

Call `mcp__claude_ai_Atlassian__searchJiraIssuesUsingJql` with:
- `jql`: `"Epic Link" = <epic-key> OR parent = <epic-key> ORDER BY created DESC` (if the ticket has an epic link)
- `fields`: ["summary", "status", "issuetype", "description"]
- `maxResults`: 15
- `responseContentFormat`: "markdown"

Also search for recently completed tickets in the same project that may be related:
- `jql`: `project = <project-key> AND status = Done AND resolved >= -30d AND text ~ "<key terms from title>" ORDER BY resolved DESC`
- `maxResults`: 5

Read the summaries and descriptions of related tickets to understand the broader feature context.

---

## Phase 2: Gather Codebase Context

### 2a. Understand the landscape

Call `mcp__plugin_engineering-knowledge-base_ekb__stats` to see what repos and content are indexed.

Call `mcp__plugin_engineering-knowledge-base_ekb__tree` with:
- `depth`: 3

This gives you the overall project structure. Identify which repos and top-level directories are likely relevant to the ticket.

### 2b. Search for relevant code

Call `mcp__plugin_engineering-knowledge-base_ekb__hybrid_search` with:
- `query`: the Jira ticket title + key terms from the description
- `sources`: "code"
- `limit`: 10

Call `mcp__plugin_engineering-knowledge-base_ekb__semantic_search` with:
- `query`: a rephrased version of the problem statement (what needs to change, not the ticket title verbatim)
- `sources`: "code"
- `limit`: 10

### 2c. Find key types and interfaces

Based on the search results, identify relevant type/interface/function names. Call `mcp__plugin_engineering-knowledge-base_ekb__symbol_search` with:
- `query`: regex pattern matching key domain terms
- `kind`: "interface" (first pass), then "struct" (second pass)
- `limit`: 10

### 2d. Read critical files

For the top 3-5 most relevant files from search results, call `mcp__plugin_engineering-knowledge-base_ekb__file` with `action: "read"` to understand the current implementation.

Use `mcp__plugin_engineering-knowledge-base_ekb__chunk` to expand context around the most relevant search hits.

### 2e. Check recent changes

For files that will likely be modified, call `mcp__plugin_engineering-knowledge-base_ekb__git` with:
- `action`: "log"
- `limit`: 5

This reveals recent activity, active contributors, and the velocity of change in the affected area.

---

## Phase 3: Gather Documentation Context

### 3a. Find existing tech designs and reference architecture

Call `mcp__claude_ai_Atlassian__searchConfluenceUsingCql` with:
- `cql`: `title ~ "tech design" AND space = "<team-space>" AND type = page ORDER BY lastModified DESC`
- `limit`: 10

Also search for architecture docs:
- `cql`: `title ~ "architecture" AND space = "<team-space>" AND type = page ORDER BY lastModified DESC`
- `limit`: 5

### 3b. Search for organizational context

Call `mcp__plugin_zoe_zoe__search` with:
- `query`: key terms from the ticket (e.g., the service name, the domain concept)
- `mode`: "hybrid"
- `limit`: 5

This surfaces organizational knowledge, standards, and guidelines relevant to the tech design.

---

## Phase 4: Generate the Tech Design

Read `templates/tech-design-template.md` (relative to this skill's directory) for the template structure. This is the single canonical template — use it as-is.

Also read `templates/org-standards-reference.md` and `templates/tech-design-review-checklist.md` from this skill's directory for quality standards and architectural constraints.

### Auto-fill rules

| Field | Value |
|-------|-------|
| Task ID | Jira ticket key |
| Developer | Jira assignee name (from Phase 1a) |
| Reviewers | Leave blank for humans to fill |
| Version | 1.0 |
| Goal | Synthesized from Jira description — the problem being solved and why |
| Requirements | Derived from Jira acceptance criteria |

### Section-level guidance

**Scope & Constraints**: Derive In Scope from acceptance criteria, Out of Scope from what related tickets cover or what the description explicitly excludes, Constraints from comments and organizational context.

**General Flow**: Generate a mermaid diagram (`graph TD` or `sequenceDiagram`) that shows the data/request flow through the affected components. Highlight components that need development. Follow with a brief text description.

**Architecture → Current State**: Ground this in actual code discovered in Phase 2. Reference specific file paths, function names, interfaces, and data flows. Do not be generic.

**Architecture → Proposed Changes**: Describe the high-level approach first, then break down per-component in the Component Breakdown subsection. For each component: current behavior → proposed behavior, plus any API/interface/data model changes.

**Database Services**: Only include if the ticket involves database schema changes, new tables, or significant query changes. Omit if not relevant.

**Backend Technologies**: List languages, frameworks, and libraries relevant to the change. Focus on what's being used or introduced, not a full inventory.

**Outsourced Services**: Only include if the ticket involves third-party or external service integrations. Omit if not relevant.

**Alternatives Considered**: At least 2 alternatives with pros, cons, and reasoning for rejection.

**Security Considerations**: Address three sub-questions: (1) What auth does this require — do new endpoints need middleware? (2) What data is sensitive — PII, partner IDs, credentials? (3) What's the attack surface — client-side code, redirect URLs, public endpoints? If the change has no security implications, state why in one sentence.

**Edge Cases & Failure Modes**: At least 3 edge cases in the table format. Include at least one from each category: data consistency, error handling, concurrency/race conditions.

**Failure Modes & Recovery**: For each downstream dependency the change touches, fill the table: what happens if unavailable, how is failure detected, how does recovery work. Address async failure visibility (how do we know async jobs failed?), retry policy (retryable vs non-retryable, max attempts, backoff), and recovery after outage. If the change has no failure mode implications, state why in one sentence.

**Capacity & Performance**: Provide at least back-of-napkin estimates: expected volume (partners, req/s at peak), data growth rate, resource profile (Redis memory, DB connections, queue depth at peak), and known limits (item size, message size, timeouts). "~100 req/s at peak across 500 partners" is sufficient. If the change is low-traffic, state the expected volume briefly.

**Benchmark and Cost Estimation**: Include if the change has infrastructure cost or performance implications. Omit for small changes with no cost impact.

**Testing Strategy**: Specific test scenarios — unit tests, integration tests, manual verification. Reference actual test files from the codebase where patterns exist.

**Monitoring and Alarms**: Describe what to monitor for the change. New metrics, alert thresholds, dashboard updates.

**Migration & Rollback**: Concrete deployment plan. Feature flags, phased rollout, rollback steps. Not just "revert the PR".

**Open Questions**: Genuine unknowns that need team discussion or PM input. Not things answerable from the gathered context.

**Appendix**: Links to Jira ticket, related tech designs, architecture docs, relevant PRs.

---

## Phase 5: Self-Validation

Before publishing, verify the tech design meets quality thresholds:

### Completeness Checks

- [ ] All included sections are filled (no "TBD" or empty sections — omit the section entirely if not relevant)
- [ ] Goal is specific, not a copy-paste of the Jira ticket
- [ ] Current State references actual code (file paths, function names)
- [ ] At least 2 alternatives are documented
- [ ] At least 3 edge cases are identified
- [ ] Edge cases include at least one from each category: data consistency, error handling, concurrency/race conditions
- [ ] Security Considerations addresses auth, sensitive data, and attack surface (or states N/A with rationale)
- [ ] Failure Modes & Recovery lists each downstream dependency with unavailability behavior (or states N/A with rationale)
- [ ] Capacity & Performance includes at least one quantified estimate (or states N/A with rationale)
- [ ] Testing strategy includes specific test scenarios, not generic statements
- [ ] Rollback strategy is concrete (not "revert the PR")
- [ ] Open questions are genuine unknowns, not things answerable from the gathered context
- [ ] Mermaid diagram in General Flow is syntactically valid and reflects the actual proposed flow

### Quality Checks

- [ ] File paths and function names referenced in the tech design actually exist in the codebase (verified via search results)
- [ ] The proposed solution is consistent with existing architectural patterns found in the codebase
- [ ] No acceptance criteria from the Jira ticket are unaddressed in the tech design

If any check fails, revise the relevant section before proceeding.

---

## Phase 6: Publish to Confluence

Call `mcp__claude_ai_Atlassian__createConfluencePage` with:
- `title`: "[TECH DESIGN] <TICKET-KEY>: <Jira Ticket Title>"
- `body`: the full tech design document in markdown
- `contentFormat`: "markdown"
- `spaceId`: the team's Confluence space ID (found during Phase 3 searches)
- `parentId`: the "Tech Designs" parent page ID (found during Phase 3 searches; if not found, create at the space root level)

Record the returned page URL.

---

## Phase 7: Link Back to Jira

Call `mcp__claude_ai_Atlassian__addCommentToJiraIssue` with:
- `issueIdOrKey`: the source ticket key
- `commentBody`: "Tech design draft generated and published to Confluence: <page-URL>\n\nThis is an AI-generated draft. Please review, refine, and update status to REVIEWED when complete.\n\nKey decisions to validate:\n<list 2-3 most important design decisions>\n\nOpen questions:\n<list open questions from the design>"
- `contentFormat`: "markdown"

---

## Phase 8: Notify (Optional)

If the user appended `--slack` or asked to notify the team:

1. Call `mcp__plugin_slack_slack__slack_search_channels` with `query`: "kraken" or the team name to find the team's tech design channel.
2. Call `mcp__plugin_slack_slack__slack_send_message` with:
   - `channel_id`: the channel found above
   - `message`: "New tech design draft published for <TICKET-KEY>: <Title>\n<page-URL>\n\nPlease review and add comments on the Confluence page."

---

## Final Output to Terminal

After all phases complete, print a summary:

```
## Tech Design Generated

**Ticket**: <TICKET-KEY> -- <Title>
**Confluence**: <page-URL>
**Jira comment**: Posted

### Tech Design Summary
<2-3 sentence summary of the proposed solution>

### Key Decisions Needing Review
<Numbered list of the most important decisions>

### Open Questions
<Numbered list from the tech design>

### Impacted Modules
<Bulleted list of affected code areas>
```
