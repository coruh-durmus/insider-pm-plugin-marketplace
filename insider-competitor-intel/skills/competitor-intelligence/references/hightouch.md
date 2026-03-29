# Hightouch — Competitor Reference

## Overview
Hightouch is a Composable CDP and reverse ETL platform founded in 2020. Headquartered in San Francisco. Backed by Y Combinator, Amplify Partners, and others. Targets data-mature companies (mid-market to enterprise) that want to activate their data warehouse as the source of truth for marketing and operations.

## Documentation URLs
| Resource | URL |
|---|---|
| Main Documentation | https://hightouch.com/docs |
| Getting Started | https://hightouch.com/docs/getting-started/welcome |
| Composable CDP Overview | https://hightouch.com/docs/getting-started/cdp |
| Customer Studio (Segmentation) | https://hightouch.com/docs/customer-studio/overview |
| AI Decisioning (AID) | https://hightouch.com/docs/ai-decisioning/overview |
| Reverse ETL / Syncs | https://hightouch.com/docs/syncs/overview |
| Hightouch Events | https://hightouch.com/docs/events/overview |
| Destinations Overview | https://hightouch.com/docs/destinations/overview |
| API Guide | https://hightouch.com/docs/developer-tools/api-guide |
| API Reference | https://hightouch.com/docs/api-reference |
| CLI Guide | https://hightouch.com/docs/developer-tools/cli-guide |
| Changelog | https://hightouch.com/changelog |

## Positioning
"Hightouch is the Composable CDP — sync customer data from your warehouse to 200+ tools." Positions as the data activation layer for warehouse-native companies. Increasingly pushing into AI Decisioning (next-best-action) as a differentiator beyond pure reverse ETL.

## Core Product Areas

### Products
1. **Reverse ETL** — sync models/queries from your data warehouse to any destination
2. **Customer Studio** — no-code audience builder on top of warehouse data
3. **AI Decisioning** — next-best-action engine using ML models to personalize at scale
4. **Journeys** — multi-step, cross-channel orchestration (newer product)
5. **Events** — event streaming and enrichment

### Channels
- Not a channel platform itself — syncs audiences/events TO channels (Braze, Klaviyo, Salesforce, etc.). Native channel execution is limited; focus is data activation.
- Journeys feature does have some direct execution capability via connected destinations.

### Segmentation
- Customer Studio: visual audience builder on warehouse data (SQL models, no-code). Computed traits, predictive models can be added via dbt or custom ML.

### AI & Personalization
- AI Decisioning: surfaces personalized experiences by running ML models (propensity, affinity, lookalike) at decision time. No-code interface to define goals and guardrails.

### Data Model
- Warehouse-first: BigQuery, Snowflake, Databricks, Redshift, PostgreSQL as sources. No proprietary data store — your warehouse IS the CDP.

### Developer Experience
- REST API, CLI, Git-based version control for syncs and models. Terraform provider for infrastructure-as-code. Strong developer tooling.

### Integrations / Destinations
- 200+ destinations: CRMs (Salesforce, HubSpot), MAPs (Braze, Klaviyo, Iterable, Marketo), Ad platforms (Facebook, Google, LinkedIn, TikTok), Data warehouses, Support tools (Zendesk, Intercom), and more.

## Strengths
- Best-in-class reverse ETL — considered the category leader
- Composable architecture appeals to data-mature, engineering-heavy teams
- AI Decisioning is genuinely innovative for warehouse-native personalization
- Strong developer experience (Terraform, Git sync, CLI)
- Warehouse-first model avoids data duplication and vendor lock-in
- Rapidly expanding product (Journeys, Events, AI Decisioning added in 2023–2024)

## Weaknesses
- Not a standalone engagement platform — requires other tools for message execution
- Total stack cost is high (warehouse + Hightouch + channel tools)
- Customer Studio requires data in a warehouse first — not for data-immature orgs
- No native mobile/email/push sending capabilities
- Journey orchestration is newer and less mature than Insider/Braze/MoEngage

## Pricing Model
Workspace-based subscription + usage tiers by sync volume and destinations. Free tier available. Mid-market and enterprise plans require custom quotes.

## Key Differentiators vs. Insider One
- Hightouch is a complementary layer, not a full replacement for Insider — often stacked together
- Companies that choose Hightouch as a "CDP" still need a separate MAP/engagement tool
- Insider One is an all-in-one platform; Hightouch requires assembling a stack
- Insider's CDP is operational (powers real-time messaging); Hightouch is analytical-to-operational sync
- Hightouch wins with engineering-led orgs that have an existing warehouse investment

## Recommended Talking Points (Insider vs. Hightouch)
- "Hightouch is a data pipeline tool — you still need Insider (or Braze) to actually communicate with customers"
- "Insider's CDP is built for real-time engagement, not batch warehouse sync — faster time to personalization"
- "With Insider One you don't need a separate warehouse layer to get unified customer profiles operational"
- "For brands without a mature data engineering team, Insider's all-in-one approach delivers faster ROI"
