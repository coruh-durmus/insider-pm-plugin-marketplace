# Insider PM Knowledge Hub

A Claude Code plugin that gives Insider PMs a unified product knowledge assistant. Ask any product question and get a source-cited answer drawn from Confluence, Jira, codebase, Insider Academy, and Slack — validated against Hop (@hop in Slack).

## Requirements

This plugin requires the following MCP servers to be configured:

- **Atlassian MCP** — for Confluence access (read-only)
- **ekb MCP** — for Jira tickets and codebase search
- **Slack MCP** — for Slack search/read (all channels and DMs) and DM to @hop only

Web access (WebSearch/WebFetch) is needed for Insider Academy queries.

## Hop Validation

Every query is also sent to **@hop** (Insider's internal Q&A bot in Slack) in parallel. Hop's response is used as a validation layer — confirming, supplementing, or flagging discrepancies against the main answer. This gives PMs a cross-referenced result.

## Getting Started

After installing the plugin, run the setup wizard to configure your team and additional sources:

```
/setup-knowledge-hub
```

The wizard will ask for your team name and any team-specific sources (e.g., Shopify MCP, warehouse-guide). These additional sources become available to all plugins that depend on the knowledge hub.

## Commands

| Command | Description | Primary Source |
|---|---|---|
| `/setup-knowledge-hub` | Configure team name and additional sources | — |
| `/product-search <query>` | General product question | All sources |
| `/find-spec <query>` | Find specs, PRDs, docs | Confluence |
| `/academy-learn <query>` | How-to guides, learning | Academy |
| `/ticket-context <query>` | Related Jira tickets | Jira |
| `/code-insight <query>` | Implementation details | Codebase |

## Skill

The `pm-knowledge-hub` skill activates automatically when you ask product-related questions. It classifies your query using Insider's product taxonomy, searches relevant sources, and returns a unified answer with inline citations.

## Product Areas

The plugin understands these Insider product areas:

- Web Suite — on-site personalization, A/B testing, web push
- App Suite — in-app messaging, app push, mobile SDK
- Messaging Channels — email, SMS, WhatsApp, RCS, LINE
- CDP — segmentation, profile unification, audiences
- Architect — journey orchestration, cross-channel flows
- AI & Recommendations — Sirius AI, predictions, recommendations
- Analytics — funnels, cohorts, reporting
- Integrations — connectors, APIs, webhooks

## Permissions

| Source | Read | Write |
|---|---|---|
| Confluence | All pages/spaces | Never |
| Jira | All tickets | Never |
| Codebase | All files | Never |
| Academy | All articles | N/A |
| Slack | All channels and DMs | **Only DMs to @hop** |

The plugin never sends messages to any Slack channel or DM other than @hop.

## Additional Sources

After running `/setup-knowledge-hub`, you can configure team-specific sources that the knowledge hub uses based on keyword matching. For example:

- **warehouse-guide** — triggered by keywords: snowflake, bigquery, databricks, redshift
- **Shopify MCP** — triggered by keywords: shopify, shopify webhook

These sources are shared with all plugins that depend on the knowledge hub (doc-writer, pvd-writer, task-writer), so you only configure them once.

See `config/team-config.json.example` for the expected format.
