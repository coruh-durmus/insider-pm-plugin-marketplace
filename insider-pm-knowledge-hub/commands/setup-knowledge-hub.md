---
description: Configure the knowledge hub with your team name and additional data sources
allowed-tools: Read, Write
argument-hint: ""
---

Run the interactive setup wizard for the insider-pm-knowledge-hub plugin.

## Step 1 — Team Name

Ask: "What is your team name? (e.g., Kraken, WhatsApp, CDP)"

Store the answer as `team_name`.

## Step 2 — Additional Sources

Ask:
> "Besides the standard knowledge sources (Confluence, Jira, codebase, Academy, Slack, Hop), do you have any team-specific sources you'd like the knowledge hub to use?
>
> These additional sources will be available to all plugins that depend on the knowledge hub (doc-writer, pvd-writer, task-writer, etc.).
>
> For example:
> - Shopify MCP (for Shopify-related queries)
> - warehouse-guide plugin (for data warehouse queries)
> - Other MCP servers or plugins
>
> List them, or type 'none'."

**If the PM lists sources:**
For each source, ask:
> "What keywords should trigger the use of [source]? (e.g., 'snowflake, bigquery, redshift, databricks' for warehouse-guide)"

Store each source as an entry in `additional_sources` with `name`, `type` ("plugin" or "mcp"), and `keywords` (array).

**If the PM needs a source that doesn't exist:**
> "That source isn't available as a plugin or MCP server yet. You can create your own plugin using `/create-plugin` or configure an MCP server for it. Run `/setup-knowledge-hub` again once it's available."

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
> "Setup complete! Your configuration has been saved. The knowledge hub will now use your additional sources when relevant keywords are detected.
>
> All plugins that depend on the knowledge hub (doc-writer, pvd-writer, task-writer) will automatically benefit from these sources.
>
> To reconfigure at any time, run `/setup-knowledge-hub` again."
