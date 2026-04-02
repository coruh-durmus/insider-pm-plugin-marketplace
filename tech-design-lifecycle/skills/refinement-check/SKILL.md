---
name: "tech-design-lifecycle:refinement-check"
description: "Quick pre-refinement ticket analysis. Scores requirements clarity across 5 dimensions, identifies missing info, estimates complexity, and recommends Ready/Needs Clarification/Suggest Split. Completes in under 60 seconds."
---

# /refinement-check -- Pre-Refinement Ticket Analysis

You are performing a quick pre-refinement analysis of a Jira ticket. The user has provided: $ARGUMENTS

This must be FAST and CONCISE. Your entire output should be readable in under 2 minutes. Do not generate a full tech design -- this is a readiness check, not a design document.

---

## Step 1: Fetch Ticket Details

Call `mcp__claude_ai_Atlassian__getAccessibleAtlassianResources` to get the cloud ID.

Call `mcp__claude_ai_Atlassian__getJiraIssue` with:
- `issueIdOrKey`: the ticket key from user input
- `responseContentFormat`: "markdown"

Read the full ticket: summary, description, acceptance criteria, comments, story points, linked issues, labels, epic.

---

## Step 2: Assess Requirements Clarity

Score each dimension from 1-5:

| Dimension | Score Criteria |
|-----------|---------------|
| **Problem clarity** | 1 = no description, 2 = vague, 3 = clear but incomplete, 4 = clear with context, 5 = precise with examples |
| **Acceptance criteria** | 1 = none, 2 = vague bullet points, 3 = testable criteria exist, 4 = comprehensive, 5 = comprehensive with edge cases |
| **Scope definition** | 1 = unbounded, 2 = "do X" with no boundaries, 3 = in/out of scope partially defined, 4 = clear boundaries, 5 = explicit in/out scope with rationale |
| **Dependencies identified** | 1 = none mentioned, 2 = implied but not listed, 3 = some listed, 4 = all technical deps listed, 5 = technical + team + timeline deps |
| **Success measurability** | 1 = no success criteria, 2 = subjective criteria, 3 = some measurable criteria, 4 = clear metrics, 5 = metrics with targets |

Compute the **overall clarity score** as the average, rounded to 1 decimal.

---

## Step 3: Identify Missing Information

Based on the clarity assessment, list specific missing information. Be concrete -- not "needs more detail" but "the description doesn't specify what happens when the payment amount exceeds the merchant's daily limit."

---

## Step 4: Identify Impacted Modules

Run targeted code searches to find affected areas. Keep this fast -- 2-3 search calls maximum.

Call `mcp__plugin_engineering-knowledge-base_ekb__hybrid_search` with:
- `query`: the ticket title + key terms from description
- `sources`: "code"
- `limit`: 8

Optionally call `mcp__plugin_engineering-knowledge-base_ekb__symbol_search` if specific types or functions are mentioned in the ticket.

List the modules/services/files that would likely need changes.

---

## Step 5: Estimate Complexity

Based on the ticket scope and impacted modules, assess complexity:

| Factor | Assessment |
|--------|-----------|
| Number of modules affected | <count> |
| Crosses service boundaries | Yes / No |
| Requires data model changes | Yes / No / Likely |
| Requires API changes | Yes / No / Likely |
| Has external dependencies | Yes / No (list if yes) |
| Requires coordination with other teams | Yes / No (list if yes) |

**Complexity rating**: Low / Medium / High / Very High

If story points are set on the ticket, flag whether the complexity aligns with the points:
- Low = 1-2 SP
- Medium = 3-5 SP
- High = 8 SP
- Very High = 13+ SP or "should be split"

---

## Step 6: Identify Risks and Dependencies

List 3-5 key risks. Each risk should be one sentence. Focus on things the team should discuss during refinement, not generic software risks.

---

## Step 7: Produce the Readiness Report

Print the following report. Keep it tight -- this is the ONLY output the user sees.

```
# Refinement Check: <TICKET-KEY>

**Title**: <ticket title>
**Type**: <issue type> | **Points**: <story points or "unset"> | **Status**: <status>

---

## Requirements Clarity: <X.X>/5

| Dimension | Score | Notes |
|-----------|-------|-------|
| Problem clarity | <N>/5 | <1-sentence justification> |
| Acceptance criteria | <N>/5 | <1-sentence justification> |
| Scope definition | <N>/5 | <1-sentence justification> |
| Dependencies identified | <N>/5 | <1-sentence justification> |
| Success measurability | <N>/5 | <1-sentence justification> |

## Missing Information
<Numbered list of specific gaps. 0 items = "None identified.">

## Impacted Modules
<Bulleted list of modules/files with 1-line description of expected change>

## Complexity: <Low|Medium|High|Very High>
<1-sentence rationale. If story points mismatch, flag it: "Estimated High but ticket is sized at 3 SP -- consider re-estimating.">

## Risks
<Numbered list, 3-5 items, 1 sentence each>

## Recommendation: <Ready | Needs Clarification | Suggest Split>

<Ready>: "Ticket is well-defined. Proceed with refinement and estimation."
<Needs Clarification>: "Address the missing information above before committing to this ticket. Suggested owner: <PM|Tech Lead|Assignee>."
<Suggest Split>: "This ticket covers too much scope. Suggested split: <brief description of 2-3 sub-tickets>."
```

---

## Post-Report (Optional)

If the user appended `--comment` or asked to post to Jira:

Call `mcp__claude_ai_Atlassian__addCommentToJiraIssue` with:
- `issueIdOrKey`: the ticket key
- `commentBody`: the readiness report (formatted for Jira)
- `contentFormat`: "markdown"

Print: "Posted to Jira as a comment on <TICKET-KEY>."

Do NOT save to Confluence. Do NOT notify Slack. This is a lightweight, fast tool.
