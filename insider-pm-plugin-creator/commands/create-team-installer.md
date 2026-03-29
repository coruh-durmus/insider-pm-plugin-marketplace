---
description: Create a team installer plugin that installs and configures all PM plugins for your team
allowed-tools: Read, Write, Bash
argument-hint: ""
---

Create a team installer plugin for the Insider PM marketplace.

## What This Does

This command walks you through creating a one-command installer plugin for your team. The installer will:
- Install all selected PM plugins from the marketplace
- Pre-configure each plugin with your team's settings
- Let new team members set up their entire environment with a single command

## Step 1 — Team Name

Ask: "What is your team name? (e.g., Kraken, WhatsApp, CDP, Atrium)"

Store as `team_name`.

## Step 2 — Select Plugins

Read the marketplace directory to list all available plugins (excluding team installers and the plugin-creator itself).

Show the list:
> "Here are the available plugins. Which ones should your team installer include?
>
> 1. insider-pm-internal-knowledge — product knowledge assistant (Confluence, Jira, codebase, Academy, Slack, Hop)
> 2. insider-competitor-intel — competitive intelligence and benchmarking
> 3. warehouse-guide — Snowflake, BigQuery, Databricks, Redshift expertise
> 4. insider-pm-doc-writer — Confluence documentation writer
> 5. insider-pm-pvd-writer — Product Value Document creator
> 6. insider-pm-task-writer — Jira task description improver
> 7. insider-pm-plugin-creator — plugin creation tool
> 8. prismatic-guide — Prismatic.io integration expertise
>
> Type the numbers you want (e.g., '1,2,3,4,5,6,7') or 'all'."

## Step 3 — Configure Each Plugin

For each selected plugin that needs configuration, ask the PM for their team's values:

### If insider-pm-doc-writer is selected:
Ask:
> "For the doc-writer, I need your team's settings:
> 1. What is your Confluence space key? (e.g., ProductKB)
> 2. What is the parent page ID for your docs?
>    **How to find your page ID:** You can go to your parent Confluence page and ask Rovo for the page ID of your parent page.
> 3. Which Jira project(s) contain your feature tickets? (comma-separated, e.g., SD, AT)
> 4. Trigger mode — manual only or also automated? (manual/auto)
>    If auto: which Jira status triggers documentation?
> 5. Review preference — always approve or auto-publish minor updates? (always/auto)"

### If insider-pm-pvd-writer is selected:
Ask:
> "For the PVD writer, I need your team's settings:
> 1. What is the page ID of your team's PVD folder in Confluence?
>    **How to find your page ID:** You can go to your parent Confluence page and ask Rovo for the page ID of your parent page."

### If insider-pm-task-writer is selected:
Ask:
> "For the task writer, do you have any team-specific additional sources?
> For example:
> - warehouse-guide (for data warehouse tasks)
> - Shopify MCP (for Shopify tasks)
> - prismatic-guide (for Prismatic tasks)
> - Other plugins or MCPs
>
> List them with keywords, or type 'none'."

For each source, ask for keywords.

## Step 4 — Generate Installer Plugin

Use `insider-kraken-plugin-installer` as a reference template. Read its full structure from the marketplace.

Generate the installer plugin:
- **Name:** `insider-<team_name_lowercase>-plugin-installer`
- **Command:** `/install-<team_name_lowercase>-plugins`
- **Structure:**
  ```
  insider-<team>-plugin-installer/
  ├── .claude-plugin/plugin.json
  ├── skills/<team>-installer/SKILL.md
  ├── commands/install-<team>-plugins.md
  └── README.md
  ```

The generated SKILL.md should:
- Start with a marketplace check (guide PM to run `/plugin marketplace add Corcit/insider-pm-plugin-marketplace` if not already added)
- Install plugins in dependency order (dependencies first)
- For each plugin, show its purpose and config
- Wait for PM confirmation before moving to the next
- Write config files with the values collected in Step 3
- Show a completion summary table at the end
- Include guidance on keeping plugins up to date (`/plugin marketplace update insider-pm-plugin-marketplace` + `/reload-plugins`)

## Step 5 — Review

Show the PM the generated file tree and each file's content. Ask for approval.

## Step 6 — Publish to Marketplace

After approval:

1. Create the plugin files in the `Plugins/` directory
2. Register it in `.claude-plugin/marketplace.json`
3. Add it to the Team Installers table in `README.md`
4. Commit the changes:
   ```bash
   git add <plugin-directory>/ .claude-plugin/marketplace.json README.md
   git commit -m "feat: add <plugin-name>"
   ```
5. Push to the marketplace:
   ```bash
   git push origin main
   ```

Then tell the PM:
> "Your team installer is ready! Here's how your team members can use it:
>
> **First-time setup (one-time):**
> ```
> /plugin marketplace add Corcit/insider-pm-plugin-marketplace
> /plugin install <plugin-name>@insider-pm-plugin-marketplace
> /reload-plugins
> /install-<team>-plugins
> ```
>
> Share these 4 commands with your team. They'll have everything set up in minutes."
