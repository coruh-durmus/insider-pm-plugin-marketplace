---
description: Compare a specific feature across competitors and Insider One
allowed-tools: WebFetch, WebSearch, Read
argument-hint: "<feature-area> [competitor-name or all]"
---

Compare the feature area "$1" across Insider One and competitors.

## Step 1 — Determine Competitors
If a specific competitor is named as $2, include only that competitor plus Insider One.
If $2 is "all" or not specified, include all 9 competitors: Braze, MoEngage, Bloomreach, Klaviyo, Hightouch, Census (Fivetran Activations), Salesforce Marketing Cloud, CleverTap, Iterable.

## Step 2 — Map Feature Area
Identify which benchmark dimension(s) best match "$1":
- "CDP" → CDP Capabilities
- "AI" or "personalization" → AI & Personalization
- "channels" or "messaging" → Channel Coverage
- "segmentation" → Segmentation Depth
- "journeys" or "orchestration" → Journey Orchestration
- "analytics" or "reporting" → Analytics & Reporting
- "pricing" → Pricing Model
- "developer" or "API" or "SDK" → Developer Experience
- "integrations" or "connectors" → Integrations Ecosystem
- "enterprise" or "compliance" or "security" → Enterprise Readiness
- "WhatsApp" → Channel Coverage (WhatsApp specifically)
- "email" → Channel Coverage (Email specifically)
- "push" → Channel Coverage (Push Notifications)
- "web personalization" or "onsite" → AI & Personalization + Channel Coverage

Load the relevant reference file(s) from `${CLAUDE_PLUGIN_ROOT}/skills/competitor-intelligence/references/` for each competitor.

## Step 3 — Feature Comparison Table
Produce a detailed comparison table with columns: Competitor | Capability Level | Key Details | Insider One Advantage/Gap

Use these capability levels: ⭐⭐⭐ Best-in-class | ⭐⭐ Strong | ⭐ Basic | — Not available

Example output format:
| Platform | $1 Capability | Details | vs. Insider One |
|---|---|---|---|
| Insider One | ⭐⭐⭐ | [Details] | — |
| Braze | ⭐⭐ | [Details] | [Gap or advantage] |
| ... | | | |

## Step 4 — Narrative Analysis
Write a 3–5 paragraph narrative analysis of "$1" across the competitive landscape, covering:
- Which platforms lead in this capability and why
- Where Insider One stands relative to the category
- Recent notable developments in this feature area (fetch from live docs if possible)
- Any emerging trends or threats in this feature area

## Step 5 — Implications for Insider One
List 2–3 strategic implications or recommendations based on this feature comparison. Flag if any competitor appears to be investing heavily in an area where Insider One may have a gap.
