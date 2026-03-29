---
description: Benchmark a competitor against Insider One
allowed-tools: WebFetch, WebSearch, Read
argument-hint: "<competitor-name> [focus-area]"
---

Benchmark the competitor "$1" against Insider One across the standard competitive dimensions.

## Step 1 — Load Competitor Knowledge
Read the relevant reference file for "$1" from `${CLAUDE_PLUGIN_ROOT}/skills/competitor-intelligence/references/`. Match the competitor name to the correct file:
- braze → braze.md
- moengage → moengage.md
- bloomreach → bloomreach.md
- klaviyo → klaviyo.md
- hightouch → hightouch.md
- census or fivetran → census.md
- salesforce or sfmc → salesforce-mc.md
- clevertap → clevertap.md
- iterable → iterable.md

If $1 is not recognized, tell the user the supported competitors: Braze, MoEngage, Bloomreach, Klaviyo, Hightouch, Census (Fivetran Activations), Salesforce Marketing Cloud, CleverTap, Iterable.

## Step 2 — Fetch Live Data (Optional but Recommended)
If network access is available, fetch the competitor's release notes or changelog URL from the reference file to check for any capabilities added in the last 90 days. Mention if the live data was consulted.

## Step 3 — Benchmark Matrix
Produce a benchmark comparison table using these 10 dimensions. Rate each on a scale of: ✅ Strong | ⚠️ Partial | ❌ Weak/Missing — for both Insider One and the competitor.

| Dimension | Insider One | $1 | Notes |
|---|---|---|---|
| Channel Coverage | | | |
| AI & Personalization | | | |
| CDP Capabilities | | | |
| Segmentation Depth | | | |
| Journey Orchestration | | | |
| Analytics & Reporting | | | |
| Developer Experience | | | |
| Integrations Ecosystem | | | |
| Pricing Model | | | |
| Enterprise Readiness | | | |

If a focus area was specified as $2, add a deeper section covering that specific dimension with more detail.

## Step 4 — Summary
Write a 3–5 sentence competitive summary covering:
- Where $1 is stronger than Insider One
- Where Insider One is stronger than $1
- The deal scenario where Insider is most likely to win vs. lose

## Step 5 — Recommended Talking Points
List 3–5 concise talking points Insider's sales team should use when competing against $1.
