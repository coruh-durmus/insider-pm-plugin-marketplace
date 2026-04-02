---
name: "tech-design-lifecycle:brainstorm"
description: "Structured 6-phase design brainstorming with MCP-powered context. Accepts a Jira ticket, freeform topic, or Confluence page. Produces problem analysis, landscape scan, solution approaches, edge cases, comparison matrix, and recommendation."
---

# /brainstorm -- Structured Tech Design Brainstorming

You are facilitating a structured brainstorming session. The user has provided a topic: $ARGUMENTS

This could be a Jira ticket key (e.g., "SD-12345"), a freeform problem statement (e.g., "How should we handle rate limiting?"), or a reference to a Confluence page. Parse the input to determine which type it is.

Follow the 6-phase structure below. Complete each phase fully before moving to the next. Print each phase's output as you go -- this is an interactive, conversational skill.

---

## Phase 1: Problem Unpacking

### If the input is a Jira ticket key:

Call `mcp__claude_ai_Atlassian__getAccessibleAtlassianResources` to get the cloud ID.

Call `mcp__claude_ai_Atlassian__getJiraIssue` with:
- `issueIdOrKey`: the ticket key
- `responseContentFormat`: "markdown"

Extract the problem from the ticket description, acceptance criteria, and comments.

### If the input is freeform text:

Use the text directly as the problem statement. No MCP calls needed yet.

### If the input references a Confluence page:

Call `mcp__claude_ai_Atlassian__searchConfluenceUsingCql` to find the page, then `mcp__claude_ai_Atlassian__getConfluencePage` with `contentFormat: "markdown"` to read it.

### Output Phase 1:

```
---
## Phase 1: Problem Unpacking

### Problem Statement
<Restate the problem in your own words. Be specific. If from Jira, do NOT copy-paste -- synthesize and clarify.>

### Success Criteria
<What does "done" look like? 3-5 measurable or verifiable criteria.>

### Constraints
<Known technical, timeline, organizational, or resource constraints. Include any discovered from Jira comments or related tickets.>

### Assumptions
<What are we assuming to be true? List explicitly so the team can challenge them.>
---
```

Pause briefly after printing Phase 1 to let the output render, then continue immediately to Phase 2.

---

## Phase 2: Landscape Scan

Search for existing patterns, prior art, and relevant context in the codebase and documentation.

### 2a. Code search

Call `mcp__plugin_engineering-knowledge-base_ekb__hybrid_search` with:
- `query`: the problem statement (synthesized, not raw)
- `sources`: "code"
- `limit`: 10

Call `mcp__plugin_engineering-knowledge-base_ekb__semantic_search` with:
- `query`: "how does <the system> currently handle <the problem domain>"
- `sources`: "code,docs"
- `limit`: 10

### 2b. Find related patterns

Call `mcp__plugin_engineering-knowledge-base_ekb__symbol_search` with:
- `query`: regex for key domain terms from the problem
- `limit`: 10

### 2c. Search for prior Tech designs

Call `mcp__claude_ai_Atlassian__searchConfluenceUsingCql` with:
- `cql`: `text ~ "<key terms>" AND type = page ORDER BY lastModified DESC`
- `limit`: 5

### 2d. Check organizational knowledge

Call `mcp__plugin_zoe_zoe__search` with:
- `query`: key terms from the problem
- `limit`: 5

### Output Phase 2:

```
---
## Phase 2: Landscape Scan

### Existing Patterns Found
<For each relevant pattern found in the codebase:>
- **<Pattern name>** (<file path>): <1-sentence description of how it works and why it's relevant>

### Prior Art
<Related tech designs, ADRs, or documentation found in Confluence:>
- **<Doc title>**: <1-sentence summary and relevance>

### Key Observations
<3-5 observations about the current state that will influence solution tech design.>
---
```

---

## Phase 3: Solution Generation

Generate 2-4 distinct solution approaches. Each approach must be meaningfully different -- not variations of the same idea with minor parameter changes.

For each approach, consider:
- How it fits with existing code patterns found in Phase 2
- Build vs. extend existing components
- Complexity vs. completeness trade-off

### Output Phase 3:

```
---
## Phase 3: Solution Approaches

### Approach A: <Name>
**Summary**: <2-3 sentences describing the approach>

**How it works**:
<Numbered steps of the approach. Reference specific existing components where applicable.>

**Pros**:
- <Concrete advantage, referencing the problem's constraints>

**Cons**:
- <Concrete disadvantage>

**Complexity**: <Low / Medium / High>
**Estimated effort**: <T-shirt size: S/M/L/XL>

---
<Repeat for Approaches B, C, and optionally D>
---
```

---

## Phase 4: Edge Case Hunting

Systematically identify failure modes and edge cases. Do NOT just list generic concerns -- make them specific to this problem and the approaches proposed.

Walk through each of these categories:

1. **Data consistency**: What happens if data is partially written? Race conditions between concurrent operations?
2. **Error handling**: What happens when external services fail? Timeouts? Malformed input?
3. **Scale**: What happens at 10x current load? What about empty/zero states?
4. **Security**: Authentication bypass? Injection? Data leakage? Privilege escalation?
5. **Backward compatibility**: Does this break existing clients, APIs, or data formats?
6. **Operational**: How do you debug this in production? What happens during deployment? Can you feature-flag it?
7. **Integration boundaries**: What happens at the boundary between this service and its dependencies?

### Output Phase 4:

```
---
## Phase 4: Edge Cases & Failure Modes

| # | Category | Scenario | Impact | Affected Approaches |
|---|----------|----------|--------|---------------------|
| 1 | Data consistency | ... | ... | A, B |
| 2 | Error handling | ... | ... | A |
| 3 | ... | ... | ... | ... |

### Critical Edge Cases (must be handled)
<Top 3-5 edge cases that are non-negotiable. Any viable solution must address these.>

### Tech Design-Dependent Edge Cases
<Edge cases that only apply to specific approaches. These may influence the final choice.>
---
```

---

## Phase 5: Convergence

Compare the approaches against each other using the edge cases and constraints identified above.

### Output Phase 5:

```
---
## Phase 5: Comparison & Recommendation

### Comparison Matrix

| Dimension | Approach A | Approach B | Approach C |
|-----------|-----------|-----------|-----------|
| Implementation complexity | ... | ... | ... |
| Handles critical edge cases | ... | ... | ... |
| Performance characteristics | ... | ... | ... |
| Maintainability | ... | ... | ... |
| Timeline to deliver | ... | ... | ... |
| Risk level | ... | ... | ... |
| Fits existing patterns | ... | ... | ... |

### Recommendation

**Recommended approach: <Name>**

<3-5 sentences explaining why this approach is recommended. Reference specific edge cases it handles well, how it fits existing patterns, and why the trade-offs are acceptable.>

### What to watch out for
<2-3 specific risks with the recommended approach and how to mitigate them.>
---
```

---

## Phase 6: Output Generation

### Output Phase 6:

```
---
## Summary

### Decision to Make
<The core decision the team needs to make, stated as a clear question.>

### Recommended Answer
<1-2 sentence recommendation.>

### Open Questions for Team Discussion
<Numbered list of things this brainstorm could not resolve -- decisions that need team input, unknowns that need investigation, dependencies that need confirmation.>

### Suggested Next Steps
1. <First concrete action>
2. <Second concrete action>
3. <Third concrete action>
---
```

### Save to Confluence (if requested)

If the user appended `--save` or `--confluence` or asked to save:

Call `mcp__claude_ai_Atlassian__createConfluencePage` with:
- `title`: "[BRAINSTORM] <TICKET-KEY or short title>: <Problem summary>"
- `body`: all 6 phases combined into a single document
- `contentFormat`: "markdown"
- `spaceId`: the team's Confluence space ID
- `parentId`: the "Tech Designs" or "Brainstorming" parent page ID (search for it)

Call `mcp__claude_ai_Atlassian__addCommentToJiraIssue` (if a Jira ticket was the input) with a link to the Confluence page.

### Notify Slack (if requested)

If the user appended `--slack`:

Call `mcp__plugin_slack_slack__slack_search_channels` to find the team channel.
Call `mcp__plugin_slack_slack__slack_send_message` with a summary and link.
