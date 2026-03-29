---
description: Interactive setup wizard to customize the task writer plugin for your team
allowed-tools: Read, Write
argument-hint: ""
---

Run the interactive setup wizard for the insider-pm-task-writer plugin.

## Step 0 — Dependency Check

Before starting the wizard, verify that both required plugins are installed.

Check for these files in sibling plugin directories:
1. `insider-pm-knowledge-hub/.claude-plugin/plugin.json`
2. `insider-competitor-intel/.claude-plugin/plugin.json`

**If both found:** Proceed to Step 1.

**If `insider-pm-knowledge-hub` is missing:** Stop and show:
> "This plugin requires `insider-pm-knowledge-hub` to be installed. Please install it first:
> ```
> claude plugin install insider-pm-knowledge-hub@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-task-writer` again."

**If `insider-competitor-intel` is missing:** Stop and show:
> "This plugin requires `insider-competitor-intel` to be installed. Please install it first:
> ```
> claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-task-writer` again."

Wait for the PM to confirm they've installed the missing plugin(s). Re-check before proceeding.

## Step 1 — Knowledge Hub Config Check

Check if `insider-pm-knowledge-hub` has been configured (look for `insider-pm-knowledge-hub/config/team-config.json`).

**If not configured:** Tell the PM:
> "The knowledge hub hasn't been configured yet. Please run `/setup-knowledge-hub` first to set up your team name and additional data sources. Those settings will be shared with this plugin automatically.
>
> Once configured, run `/setup-task-writer` again."

**If configured:** Read the knowledge hub config and confirm:
> "I found your knowledge hub configuration:
> - Team: [team_name]
> - Additional sources: [list or 'none']
>
> This plugin will use these settings. Setup complete! You can now use:
> - `/improve-task` to improve a single task
> - `/improve-backlog` to improve multiple tasks from a backlog or sprint
>
> To change your team name or additional sources, run `/setup-knowledge-hub`."

IMPORTANT: The file must start with the YAML frontmatter block (starting with ---). Create directories if needed. Do NOT commit. Report status when done.