---
description: Interactive setup wizard to customize the PVD writer plugin for your team
allowed-tools: Read, Write, WebFetch, WebSearch
argument-hint: ""
---

Run the interactive setup wizard for the insider-pm-pvd-writer plugin.

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
> Once installed, run `/setup-pvd-plugin` again."

**If `insider-competitor-intel` is missing:** Stop and show:
> "This plugin requires `insider-competitor-intel` to be installed. Please install it first:
> ```
> claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-pvd-plugin` again."

Wait for the PM to confirm they've installed the missing plugin(s). Re-check before proceeding. Do not continue until both dependencies are verified.

## Step 1 — Team Name

Ask: "What is your team name? (e.g., Kraken, WhatsApp, CDP)"

Store the answer as `team_name`.

## Step 2 — PVD Parent Page

Ask: "What is the page ID or URL of your team's PVD folder in Confluence?"
> (e.g., the "Kraken PVDs" page under Product Knowledge Base → Product Value Documents → CDP PVDs → Kraken PVDs)

Store the answer as `pvd_parent_page_id`.

Then use Atlassian MCP tools to read the child pages under that parent page. Show a preview:
> "I found [N] existing PVDs under '[Page Title]'. Here are a few:
> - [PVD 1 title]
> - [PVD 2 title]
> - [PVD 3 title]
> ...
> Is this the correct location?"

If the PM says no, ask for the correct parent page again.

## Step 3 — Save Config

Write the collected answers to `${CLAUDE_PLUGIN_ROOT}/config/team-config.json`:

```json
{
  "team_name": "<team_name>",
  "pvd_parent_page_id": "<pvd_parent_page_id>"
}
```

Confirm to the PM:
> "Setup complete! Your configuration has been saved. You can now use `/create-pvd` to create Product Value Documents.
>
> To reconfigure at any time, run `/setup-pvd-plugin` again."

IMPORTANT: The file must start with the YAML frontmatter block (starting with ---). Create the commands directory if needed. Do NOT commit. Report status when done.
