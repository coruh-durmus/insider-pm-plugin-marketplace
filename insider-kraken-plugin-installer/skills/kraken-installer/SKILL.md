---
name: kraken-installer
description: >
  Use this skill when the user asks to "install Kraken plugins", "set up Kraken tools",
  "configure Kraken PM environment", or any request to install and configure the
  Insider PM plugin suite for the Kraken team.

  <example>
  Context: New Kraken PM wants to set up their environment
  user: "/install-kraken-plugins"
  assistant: "I'll install and configure all Insider PM plugins for the Kraken team. Let's go through each one."
  <commentary>
  Installs all plugins in dependency order, shows config for each, PM confirms before next.
  </commentary>
  </example>

version: 0.1.0
---

# Kraken Team Plugin Installer

You install and configure all Insider PM plugins for the Kraken team. You install plugins in dependency order, show the pre-configured settings for each, and wait for PM confirmation before proceeding to the next.

## Step 0 — Marketplace Check

First, check if the Insider PM marketplace is already added. If the PM can install plugins from it, proceed. If not, guide them:

> "Before we start, let's make sure the Insider marketplace is set up. Please run:
> ```
> /plugin marketplace add Corcit/insider-pm-plugin-marketplace
> ```
> Then come back and run `/install-kraken-plugins` again."

Wait for confirmation before proceeding.

## Installation Order

Install plugins in this order (dependencies first):

### 1. insider-pm-internal-knowledge (no config needed)

Install:
```bash
claude plugin install insider-pm-internal-knowledge@insider-pm-plugin-marketplace
```

Tell the PM:
> "Installed **insider-pm-internal-knowledge** — unified product knowledge assistant. Searches Confluence, Jira, codebase, Academy, Slack, and Hop.
> No configuration needed. Ready to use with `/product-search`, `/find-spec`, `/academy-learn`, `/ticket-context`, `/code-insight`.
>
> Continue to the next plugin?"

Wait for confirmation.

### 2. insider-competitor-intel (no config needed)

Install:
```bash
claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
```

Tell the PM:
> "Installed **insider-competitor-intel** — competitive intelligence and benchmarking.
> No configuration needed. Ready to use with `/competitive-brief`, `/benchmark`, `/feature-compare`, `/release-tracker`.
>
> Continue to the next plugin?"

Wait for confirmation.

### 3. warehouse-guide (no config needed)

Install:
```bash
claude plugin install warehouse-guide@insider-pm-plugin-marketplace
```

Tell the PM:
> "Installed **warehouse-guide** — expert assistant for Snowflake, BigQuery, Databricks, and Redshift integrations.
> No configuration needed.
>
> Continue to the next plugin?"

Wait for confirmation.

### 4. insider-pm-doc-writer (Kraken config)

Install:
```bash
claude plugin install insider-pm-doc-writer@insider-pm-plugin-marketplace
```

Show the Kraken config:
> "Installed **insider-pm-doc-writer** — Confluence documentation writer.
>
> I'll configure it with these Kraken settings:
> - **Team:** Kraken
> - **Confluence Space:** ProductKB
> - **Parent Page ID:** 3481600323
> - **Jira Projects:** SD
> - **Trigger:** Manual only
> - **Review:** Always approve
>
> Apply this configuration?"

If confirmed, write to the plugin's config directory `insider-pm-doc-writer/config/team-config.json`:

```json
{
  "team_name": "Kraken",
  "confluence_space": "ProductKB",
  "confluence_parent_page_id": "3481600323",
  "jira_projects": ["SD"],
  "trigger": {
    "mode": "manual_only",
    "auto_jira_status": null
  },
  "review": "always_approve"
}
```

Tell the PM: "Configured. Use `/write-docs` to create or update documentation. Continue to the next plugin?"

Wait for confirmation.

### 5. insider-pm-pvd-writer (Kraken config)

Install:
```bash
claude plugin install insider-pm-pvd-writer@insider-pm-plugin-marketplace
```

Show the Kraken config:
> "Installed **insider-pm-pvd-writer** — Product Value Document creator.
>
> I'll configure it with these Kraken settings:
> - **Team:** Kraken
> - **PVD Parent Page ID:** 4087152775
>
> Apply this configuration?"

If confirmed, write to the plugin's config directory `insider-pm-pvd-writer/config/team-config.json`:

```json
{
  "team_name": "Kraken",
  "pvd_parent_page_id": "4087152775"
}
```

Tell the PM: "Configured. Use `/create-pvd` to create Product Value Documents. Continue to the next plugin?"

Wait for confirmation.

### 6. insider-pm-task-writer (Kraken config)

Install:
```bash
claude plugin install insider-pm-task-writer@insider-pm-plugin-marketplace
```

Show the Kraken config:
> "Installed **insider-pm-task-writer** — Jira task description improver.
>
> I'll configure it with these Kraken settings:
> - **Team:** Kraken
> - **Additional Sources:**
>   - warehouse-guide (keywords: snowflake, bigquery, databricks, redshift, data warehouse)
>   - Shopify MCP (keywords: shopify, shopify webhook, shopify integration)
>   - prismatic-guide (keywords: prismatic, ipaas, embedded integration, custom component, connector, prismatic.io)
>
> Apply this configuration?"

If confirmed, write to the plugin's config directory `insider-pm-task-writer/config/team-config.json`:

```json
{
  "team_name": "Kraken",
  "additional_sources": [
    {
      "name": "warehouse-guide",
      "type": "plugin",
      "keywords": ["snowflake", "bigquery", "databricks", "redshift", "data warehouse"]
    },
    {
      "name": "shopify",
      "type": "mcp",
      "keywords": ["shopify", "shopify webhook", "shopify integration"]
    },
    {
      "name": "prismatic-guide",
      "type": "plugin",
      "keywords": ["prismatic", "ipaas", "embedded integration", "custom component", "connector", "prismatic.io"]
    }
  ]
}
```

Tell the PM: "Configured. Use `/improve-task` or `/improve-backlog` to improve task descriptions. Continue to the next plugin?"

Wait for confirmation.

### 7. insider-pm-plugin-creator (no config needed)

Install:
```bash
claude plugin install insider-pm-plugin-creator@insider-pm-plugin-marketplace
```

Tell the PM:
> "Installed **insider-pm-plugin-creator** — create new plugins through a guided interview.
> No configuration needed. Use `/create-plugin` to create a new plugin.
>
> All plugins installed and configured!"

## Completion

After all plugins are installed, show a summary:

> "All Kraken team plugins are installed and configured:
>
> | Plugin | Status | Config |
> |--------|--------|--------|
> | insider-pm-internal-knowledge | Installed | No config needed |
> | insider-competitor-intel | Installed | No config needed |
> | warehouse-guide | Installed | No config needed |
> | insider-pm-doc-writer | Installed + Configured | ProductKB / SD |
> | insider-pm-pvd-writer | Installed + Configured | PVD page 4087152775 |
> | insider-pm-task-writer | Installed + Configured | warehouse-guide, Shopify, prismatic |
> | insider-pm-plugin-creator | Installed | No config needed |
>
> Run `/reload-plugins` to activate all plugins.
>
> **Keeping plugins up to date:** When the marketplace is updated with new plugins or improvements, run:
> ```
> /plugin marketplace update insider-pm-plugin-marketplace
> /reload-plugins
> ```
> This pulls the latest versions of all plugins."

## Important Rules

- **Install in order.** Dependencies must be installed before dependent plugins.
- **Confirm each.** Show config and wait for PM confirmation before proceeding.
- **Pre-configured.** All configs are hardcoded for Kraken. PMs can re-run individual setup wizards later to customize.
- **Idempotent.** If a plugin is already installed, skip the install step and just verify/update the config.

Create necessary directories. Do NOT commit. Report status when done.
