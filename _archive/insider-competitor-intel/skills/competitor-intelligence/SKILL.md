---
name: competitor-intelligence
description: >
  This skill should be used when the user asks to "benchmark a competitor",
  "compare Insider to Braze", "analyze MoEngage features", "how does Klaviyo
  compare", "what does CleverTap offer", "research Iterable", "compare Insider
  One with competitors", "competitive analysis", "competitive brief",
  "what's new at Bloomreach", "Salesforce Marketing Cloud features",
  "Hightouch vs Insider", "Census reverse ETL", or any question about the
  competitive landscape for Insider One in the martech / customer engagement space.
version: 0.1.0
---

# Competitor Intelligence for Insider One

## Context

Insider One is an AI-powered omnichannel marketing and customer engagement platform. This skill loads deep knowledge about nine direct and adjacent competitors so Claude can produce accurate benchmarks, feature comparisons, competitive briefs, and release tracking reports.

## Competitor Roster

| Competitor | Category | Reference File |
|---|---|---|
| Braze | Cross-channel customer engagement | `references/braze.md` |
| MoEngage | Mobile-first customer engagement | `references/moengage.md` |
| Bloomreach | Commerce-focused CDP + engagement | `references/bloomreach.md` |
| Klaviyo | Email/SMS marketing automation | `references/klaviyo.md` |
| Hightouch | Composable CDP / reverse ETL | `references/hightouch.md` |
| Census (Fivetran Activations) | Reverse ETL / data activation | `references/census.md` |
| Salesforce Marketing Cloud | Enterprise marketing suite | `references/salesforce-mc.md` |
| CleverTap | Mobile retention & engagement | `references/clevertap.md` |
| Iterable | Cross-channel lifecycle marketing | `references/iterable.md` |

## How to Use This Skill

### For feature comparisons
1. Load the relevant competitor reference file(s) from `references/`.
2. Identify the specific feature dimension being compared (channels, segmentation, AI/ML, CDP, analytics, pricing model, etc.).
3. Structure the comparison as a table or narrative depending on the output format requested.
4. Always flag if information may be outdated — use WebSearch/WebFetch to verify against the live documentation URLs listed in each reference file.

### For release tracking
1. Visit the "Release Notes / Changelog URL" in the relevant reference file.
2. Extract the most recent releases (past 30–90 days).
3. Highlight features that could be relevant to Insider One's roadmap or sales positioning.

### For competitive briefs
1. Load all relevant reference files.
2. Follow the brief structure: Positioning → Key Features → Strengths → Weaknesses → Insider Differentiators → Recommended Talking Points.

### For benchmarking
1. Define the benchmark dimensions clearly (e.g., channel coverage, AI capabilities, data model, pricing, integrations).
2. Score or rate each competitor per dimension.
3. Produce a comparison matrix.

## Key Benchmark Dimensions

These are the standard dimensions to use when evaluating any competitor against Insider One:

- **Channel Coverage**: Email, SMS, Push (Web/App), In-App, WhatsApp, RCS, Direct Mail, Paid Ads
- **AI & Personalization**: Predictive segmentation, send-time optimization, next-best-action, generative content
- **CDP Capabilities**: Identity resolution, real-time event streaming, data unification, reverse ETL
- **Segmentation**: Behavioral, demographic, lifecycle, predictive, real-time vs. batch
- **Journey Orchestration**: Visual builder, triggers, branching logic, cross-channel coordination
- **Analytics & Reporting**: Attribution, funnel analysis, revenue impact, A/B testing
- **Developer Experience**: SDK support, API depth, webhook flexibility, headless support
- **Integrations Ecosystem**: Number and depth of native integrations, iPaaS support
- **Pricing Model**: MAU-based, contact-based, message-volume-based, platform fee
- **Enterprise Readiness**: SSO, RBAC, data residency, SOC2/ISO compliance, SLA

## Documentation Freshness

Live documentation URLs are stored in each reference file. When asked about current features or recent releases, always prefer fetching directly from those URLs over relying on the static knowledge in these files. Use `WebFetch` to pull the latest content before generating any output that claims to reflect current product state.
