---
description: Find Jira tickets related to a feature or topic
allowed-tools: Read, mcp__ekb__jira
argument-hint: "<feature or topic>"
---

Find Jira tickets and context for: $ARGUMENTS

**Source priority:** Jira first (via ekb), then Confluence and Slack.

**Entry point:** `/ticket-context`

Invoke the `pm-internal-knowledge` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-internal-knowledge/SKILL.md` with the user's query. Follow all steps in the skill, using "Jira first" as the source priority.
