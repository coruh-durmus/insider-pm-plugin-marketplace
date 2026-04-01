---
description: Find product specs, PRDs, or documentation in Confluence
allowed-tools: WebFetch, WebSearch, Read, mcp__ekb__jira
argument-hint: "<spec or PRD topic>"
---

Find product specifications and documentation for: $ARGUMENTS

**Source priority:** Confluence first, then Jira and Slack.

**Entry point:** `/find-spec`

Invoke the `pm-internal-knowledge` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-internal-knowledge/SKILL.md` with the user's query. Follow all steps in the skill, using "Confluence first" as the source priority.
