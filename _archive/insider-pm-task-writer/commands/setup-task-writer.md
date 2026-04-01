---
description: Interactive setup wizard to customize the task writer plugin for your team
allowed-tools: Read, Write
argument-hint: ""
---

Run the interactive setup wizard for the insider-pm-task-writer plugin.

## Step 0 — Dependency Check

Before starting the wizard, verify that both required plugins are installed.

Check for these files in sibling plugin directories:
1. `insider-pm-internal-knowledge/.claude-plugin/plugin.json`
2. `insider-competitor-intel/.claude-plugin/plugin.json`

**If both found:** Proceed to Step 1.

**If `insider-pm-internal-knowledge` is missing:** Stop and show:
> "This plugin requires `insider-pm-internal-knowledge` to be installed. Please install it first:
> ```
> claude plugin install insider-pm-internal-knowledge@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-task-writer` again."

**If `insider-competitor-intel` is missing:** Stop and show:
> "This plugin requires `insider-competitor-intel` to be installed. Please install it first:
> ```
> claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-task-writer` again."

Wait for the PM to confirm they've installed the missing plugin(s). Re-check before proceeding.

## Step 1 — Team Name

Ask: "What is your team name? (e.g., Kraken, WhatsApp, CDP)"

Store the answer as `team_name`.

## Step 2 — Additional Sources

Ask:
> "Besides the standard knowledge sources (Confluence, Jira, codebase, Academy, Slack, Hop) and competitor intel, do you have any team-specific sources you'd like to use when improving tasks?
>
> For example:
> - Shopify MCP (for Shopify-related tasks)
> - warehouse-guide plugin (for data warehouse tasks)
> - Other MCP servers or plugins
>
> List them, or type 'none'."

**If the PM lists sources:**
For each source, ask:
> "What keywords should trigger the use of [source]? (e.g., 'snowflake, bigquery, redshift, databricks' for warehouse-guide)"

Store each source as an entry in `additional_sources` with `name`, `type` ("plugin" or "mcp"), and `keywords` (array).

**If the PM needs a source that doesn't exist:**
> "That source isn't available as a plugin or MCP server yet. You can create your own plugin or configure an MCP server for it. Run `/setup-task-writer` again once it's available."

**If the PM types 'none':**
Set `additional_sources` to an empty array.

## Step 3 — Save Config

Write the collected answers to `${CLAUDE_PLUGIN_ROOT}/config/team-config.json`:

```json
{
  "team_name": "<team_name>",
  "additional_sources": [<sources or empty array>]
}
```

Confirm to the PM:
> "Setup complete! Your configuration has been saved. You can now use:
> - `/improve-task` to improve a single task
> - `/improve-backlog` to improve multiple tasks from a backlog or sprint
>
> To reconfigure at any time, run `/setup-task-writer` again."

IMPORTANT: The file must start with the YAML frontmatter block (starting with ---). Create directories if needed. Do NOT commit. Report status when done.