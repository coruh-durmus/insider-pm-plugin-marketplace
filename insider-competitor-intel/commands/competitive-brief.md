---
description: Generate a full competitive brief for one or more competitors
allowed-tools: WebFetch, WebSearch, Read
argument-hint: "<competitor-name(s) or all> [feature-area]"
---

Generate a structured competitive intelligence brief for: $ARGUMENTS

## Step 1 — Determine Scope
Parse the arguments to determine which competitors to cover:
- If $ARGUMENTS is "all" or blank, cover all 9 competitors: Braze, MoEngage, Bloomreach, Klaviyo, Hightouch, Census (Fivetran Activations), Salesforce Marketing Cloud, CleverTap, Iterable.
- If one or more specific competitors are named, cover only those.
- If a feature area is mentioned (e.g., "CDP", "WhatsApp", "pricing"), focus the brief on that dimension.

## Step 2 — Load Reference Files
For each competitor in scope, read the corresponding reference file from `${CLAUDE_PLUGIN_ROOT}/skills/competitor-intelligence/references/`.

## Step 3 — Brief Structure
For each competitor covered, produce the following sections:

---
### [Competitor Name]
**Category**: [e.g., Cross-channel engagement, Reverse ETL, Email automation]

**One-Line Summary**: [What they are and who they serve]

**Key Strengths**:
- [3–4 bullet points of genuine strengths]

**Key Weaknesses**:
- [3–4 bullet points of genuine weaknesses]

**Insider One Wins When**:
- [Scenarios where Insider is the better choice]

**Competitor Wins When**:
- [Scenarios where the competitor is the better choice — be honest]

**Top 3 Differentiation Points for Sales**:
1. [Talking point 1]
2. [Talking point 2]
3. [Talking point 3]
---

## Step 4 — Market Position Map (for 3+ competitors)
If three or more competitors are covered, produce a brief market positioning summary grouping competitors by their primary category:
- **Full-Stack Engagement Platforms**: (e.g., Braze, MoEngage, CleverTap, Iterable)
- **Commerce-Focused Platforms**: (e.g., Bloomreach, Klaviyo)
- **Data Activation / Composable CDP**: (e.g., Hightouch, Census/Fivetran)
- **Enterprise Suite**: (e.g., Salesforce MC)

## Step 5 — Strategic Recommendations
Write 3 strategic observations about the competitive landscape and what they mean for Insider One's positioning or product priorities.

Always conclude with a note that competitive data should be verified against live documentation links (provided in each reference file) before use in sales materials or external communications.
