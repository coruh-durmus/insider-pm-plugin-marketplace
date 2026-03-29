---
description: Interactive setup wizard to customize the doc-writer plugin for your team
allowed-tools: Read, Write, WebFetch, WebSearch
argument-hint: ""
---

Run the interactive setup wizard for the insider-pm-doc-writer plugin.

## Step 0 — Dependency Check

Before starting the wizard, verify that the `insider-pm-knowledge-hub` plugin is installed.

Search for the file `insider-pm-knowledge-hub/.claude-plugin/plugin.json` in the Plugins directory (sibling to this plugin's directory).

**If found:** Proceed to Step 1.

**If NOT found:** Stop and show:
> "This plugin requires `insider-pm-knowledge-hub` to be installed. Please install it first:
> ```
> claude plugin install insider-pm-knowledge-hub@insider-pm-plugin-marketplace
> ```
> Once installed, run `/setup-docs` again."

Wait for the PM to confirm they've installed it. Then re-check the file exists before proceeding. Do not continue until the dependency is verified.

## Step 1 — Team Name

Ask: "What is your team name? (e.g., Kraken, Holocron, DataForce)"

Store the answer as `team_name`.

## Step 2 — Confluence Space & Parent Page

Ask: "What is the Confluence space key where your product docs live? (e.g., KRAKEN)"

Store the answer as `confluence_space`.

Ask: "What is the parent page URL or ID under which your docs are organized?"

Store the answer as `confluence_parent_page_id`.

Then use Atlassian MCP tools to read the child pages under that parent page. Show a preview:
> "I found [N] pages under '[Parent Page Title]'. Here are a few:
> - [Page 1 title]
> - [Page 2 title]
> - [Page 3 title]
> ...
> Is this the correct location?"

If the PM says no, ask for the correct parent page again.

## Step 3 — Jira Project Key(s)

Ask: "Which Jira project(s) contain your feature tickets? You can list multiple, comma-separated. (e.g., SD, AT, IDEA)"

Store the answer as `jira_projects` (array).

## Step 4 — Trigger Preference

Ask:
> "How do you want to trigger documentation work?
> - A) Manual only — I'll run `/write-docs` when I need it
> - B) Manual + automated — also trigger when tickets reach a specific status"

If the PM chooses B, ask:
> "Which Jira status should trigger documentation? (e.g., Done, Ready for Docs, Released)"

Store as `trigger.mode` ("manual_only" or "manual_and_auto") and `trigger.auto_jira_status` (string or null).

Note to the PM: "You'll need to set up the automation mechanism yourself (e.g., a Claude Code hook or cron job) using this status as the trigger condition. The plugin will respond to `/write-docs <ticket-key>` when triggered."

## Step 5 — Review Preference

Ask:
> "Should I always ask for your approval before publishing?
> - A) Always ask for approval
> - B) Auto-publish minor updates, ask for major ones"

Store as `review` ("always_approve" or "auto_minor").

## Step 6 — Save Config

Write the collected answers to `${CLAUDE_PLUGIN_ROOT}/config/team-config.json`:

```json
{
  "team_name": "<team_name>",
  "confluence_space": "<confluence_space>",
  "confluence_parent_page_id": "<confluence_parent_page_id>",
  "jira_projects": ["<project1>", "<project2>"],
  "trigger": {
    "mode": "<manual_only|manual_and_auto>",
    "auto_jira_status": "<status|null>"
  },
  "review": "<always_approve|auto_minor>"
}
```

Confirm to the PM:
> "Setup complete! Your configuration has been saved. You can now use `/write-docs` to create or update documentation.
>
> To reconfigure at any time, run `/setup-docs` again."

IMPORTANT: The file must start with the YAML frontmatter block (starting with ---). Do NOT commit. Report status when done.
