---
description: Interactive setup wizard to configure insider-pm-copilot for your team
allowed-tools: Read, Write, WebFetch, WebSearch
---

# Unified Setup Wizard

Configure the insider-pm-copilot plugin for your team. This wizard walks you through all capabilities and writes a unified `team-config.json`.

## Step 1 — Team Name (Required)

Ask the PM:
> "What is your team name? (e.g., Kraken, Holocron, DataForce, WhatsApp, CDP)"

Store the answer as `team_name`.

## Step 2 — Capabilities Selection (Required)

Ask the PM:
> "Which capabilities do you want to configure? You can always come back and run `/setup` again to add more.
> - A) All (recommended for first-time setup)
> - B) Task improvement only
> - C) Documentation writing only
> - D) PVD creation only
> - E) Pick specific ones"

If the PM chooses E, ask which to configure:
> "Select which to configure:
> - Task improvement (improve Jira task descriptions)
> - Documentation writing (create/update Confluence docs)
> - PVD creation (create Product Value Documents)"

Based on the PM's selection, run the relevant steps below. Skip steps for capabilities not selected.

**Note:** If a `team-config.json` already exists, read it first. Preserve existing sections that the PM is not reconfiguring. Only overwrite sections the PM explicitly chooses to set up.

## Step 3 — Documentation Config (if selected)

### 3a — Confluence Space
> "What is the Confluence space key where your team's product documentation lives? (e.g., ProductKB, KRAKEN)"

Store as `docs.confluence_space`.

### 3b — Parent Page ID
> "What is the Confluence page ID of the parent page where docs should be created? You can find this in the page URL (the numeric ID) or paste the full URL and I'll extract it."

If the PM provides a URL, extract the numeric page ID from it.

Store as `docs.confluence_parent_page_id` (as a string).

### 3c — Jira Project Keys
> "Which Jira project(s) contain your feature tickets? (comma-separated, e.g., SD, AT, IDEA)"

Parse the response into an array. Store as `docs.jira_projects`.

### 3d — Trigger Preference
> "When should documentation be created?
> - A) Manual only — I run `/write-docs` when I'm ready
> - B) Manual + automated — also trigger when a ticket reaches a specific status"

If B:
> "What Jira status should trigger doc creation? (e.g., Done, Released, Ready for Docs)"

Store as `docs.trigger.mode` ("manual_only" or "manual_and_auto") and `docs.trigger.auto_jira_status` (status string or null).

### 3e — Review Preference
> "How should documentation be reviewed before publishing?
> - A) Always ask for my approval before any write
> - B) Auto-publish minor updates, ask for approval on major ones"

Store as `docs.review` ("always_approve" or "auto_minor").

## Step 4 — PVD Config (if selected)

### 4a — PVD Parent Page ID
> "What is the Confluence page ID of your team's PVD folder? This is the page under which new PVDs will be created.
> (Example path: Product Knowledge Base → Product Value Documents → [Team] PVDs)
> You can provide the numeric ID or paste the full URL."

If the PM provides a URL, extract the numeric page ID.

Store as `pvd.pvd_parent_page_id` (as a string).

## Step 5 — Task Improvement Config (if selected)

### 5a — Additional Sources
> "Do you have team-specific knowledge sources (plugins or MCPs) that should be consulted when improving tasks? These are invoked automatically when a task mentions relevant keywords.
>
> Examples:
> - warehouse-guide plugin (for Snowflake, BigQuery tasks)
> - Shopify MCP (for Shopify integration tasks)
> - prismatic-guide plugin (for Prismatic integration tasks)
>
> List your sources, or say 'none' to skip."

If the PM provides sources, for each one:
> "For **[source name]**: What keywords should trigger this source? (comma-separated, e.g., snowflake, bigquery, data warehouse)"

Also determine the type:
- If the source is a Claude Code plugin → type: "plugin"
- If the source is an MCP server → type: "mcp"

Store as `tasks.additional_sources` array, where each entry has `name`, `type`, and `keywords`.

If the PM says "none", store as `tasks.additional_sources: []`.

## Step 6 — Save Config

Write the unified config to `${CLAUDE_PLUGIN_ROOT}/config/team-config.json`.

The file structure:
```json
{
  "team_name": "...",
  "docs": { ... } or null,
  "pvd": { ... } or null,
  "tasks": { ... } or null
}
```

Set sections to `null` if they were skipped. If updating an existing config, merge — preserve sections the PM didn't reconfigure.

Do NOT commit to git. The config is git-ignored.

## Step 7 — Confirmation

Show a summary of what was configured:

> "**Setup complete for team [team_name]!**
>
> | Capability | Status | Key Config |
> |------------|--------|------------|
> | Internal Knowledge | Ready (no config needed) | — |
> | Competitive Intelligence | Ready (no config needed) | — |
> | Task Improvement | [Configured / Skipped] | [sources count] additional sources |
> | Documentation | [Configured / Skipped] | Space: [space], Page: [id] |
> | PVD Creation | [Configured / Skipped] | Parent: [id] |
>
> **Available commands:**
> - `/product-search`, `/find-spec`, `/academy-learn`, `/ticket-context`, `/code-insight` — Knowledge search
> - `/benchmark`, `/competitive-brief`, `/feature-compare`, `/release-tracker` — Competitive intelligence
> - `/improve-task`, `/improve-backlog` — Task improvement
> - `/write-docs` — Documentation writing
> - `/create-pvd` — PVD creation
>
> To reconfigure, run `/setup` again. Existing sections are preserved unless you choose to reconfigure them."
