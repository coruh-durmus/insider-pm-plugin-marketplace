---
description: Find Jira tickets related to a feature or topic
allowed-tools: Read, mcp__ekb__jira
argument-hint: "<feature or topic>"
---

Find Jira tickets and context for: $ARGUMENTS

**Source priority:** Jira first (via ekb), then Confluence and Slack.

**Entry point:** `/ticket-context`

Invoke the `pm-knowledge-hub` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-knowledge-hub/SKILL.md` with the user's query. Follow all steps in the skill, using "Jira first" as the source priority.
