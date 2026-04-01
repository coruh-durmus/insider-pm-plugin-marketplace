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

You install and configure all Insider PM plugins for the Kraken team. You install plugins in order, show the pre-configured settings for each, and wait for PM confirmation before proceeding to the next.

## Step 0 — Marketplace Check

First, check if the Insider PM marketplace is already added. If the PM can install plugins from it, proceed. If not, guide them:

> "Before we start, let's make sure the Insider marketplace is set up:
>
> 1. Run `/plugin marketplace add Corcit/insider-pm-plugin-marketplace`
> 2. Then run `/plugin` and select the **insider-pm-plugin-marketplace**
> 3. Select **Enable auto-update** — this keeps all your plugins up to date automatically when the marketplace is updated
>
> Once done, come back and run `/install-kraken-plugins` again."

Wait for confirmation before proceeding.

## Installation Order

Install plugins in this order:

### 1. insider-pm-copilot (Kraken config)

Install:
```bash
claude plugin install insider-pm-copilot@insider-pm-plugin-marketplace
```

Show the Kraken config:
> "Installed **insider-pm-copilot** — all-in-one PM copilot with internal knowledge, competitive intelligence, task improvement, doc writing, and PVD creation.
>
> I'll configure it with these Kraken settings:
> - **Team:** Kraken
> - **Docs — Confluence Space:** ProductKB
> - **Docs — Parent Page ID:** 3481600323
> - **Docs — Jira Projects:** SD
> - **Docs — Trigger:** Manual only
> - **Docs — Review:** Always approve
> - **PVD — Parent Page ID:** 4087152775
> - **Tasks — Additional Sources:**
>   - warehouse-guide (keywords: snowflake, bigquery, databricks, redshift, data warehouse)
>   - Shopify MCP (keywords: shopify, shopify webhook, shopify integration)
>   - prismatic-guide (keywords: prismatic, ipaas, embedded integration, custom component, connector, prismatic.io)
>
> Apply this configuration?"

If confirmed, write to the plugin's config directory `insider-pm-copilot/config/team-config.json`:

```json
{
  "team_name": "Kraken",
  "docs": {
    "confluence_space": "ProductKB",
    "confluence_parent_page_id": "3481600323",
    "jira_projects": ["SD"],
    "trigger": {
      "mode": "manual_only",
      "auto_jira_status": null
    },
    "review": "always_approve"
  },
  "pvd": {
    "pvd_parent_page_id": "4087152775"
  },
  "tasks": {
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
}
```

Tell the PM:
> "Configured. Available commands:
> - `/product-search`, `/find-spec`, `/academy-learn`, `/ticket-context`, `/code-insight` — Knowledge search
> - `/benchmark`, `/competitive-brief`, `/feature-compare`, `/release-tracker` — Competitive intel
> - `/improve-task`, `/improve-backlog` — Task improvement
> - `/write-docs` — Documentation
> - `/create-pvd` — PVD creation
>
> Continue to the next plugin?"

Wait for confirmation.

### 2. warehouse-guide (no config needed)

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

### 3. prismatic-guide (no config needed)

Install:
```bash
claude plugin install prismatic-guide@insider-pm-plugin-marketplace
```

Tell the PM:
> "Installed **prismatic-guide** — expert assistant for Prismatic.io platform and integrations.
> No configuration needed.
>
> Continue to the next plugin?"

Wait for confirmation.

### 4. insider-pm-plugin-creator (no config needed)

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
> | insider-pm-copilot | Installed + Configured | Knowledge, Intel, Docs, PVD, Tasks |
> | warehouse-guide | Installed | No config needed |
> | prismatic-guide | Installed | No config needed |
> | insider-pm-plugin-creator | Installed | No config needed |
>
> Run `/reload-plugins` to activate all plugins.
>
> **Keeping plugins up to date:** If you enabled auto-update during setup, your plugins will stay up to date automatically. If not, you can enable it anytime:
> 1. Run `/plugin`
> 2. Select **insider-pm-plugin-marketplace**
> 3. Select **Enable auto-update**"

## Important Rules

- **Install in order.** The copilot plugin must be installed first (it's the foundation).
- **Confirm each.** Show config and wait for PM confirmation before proceeding.
- **Pre-configured.** All configs are hardcoded for Kraken. PMs can re-run `/setup` later to customize.
- **Idempotent.** If a plugin is already installed, skip the install step and just verify/update the config.

Create necessary directories. Do NOT commit. Report status when done.
