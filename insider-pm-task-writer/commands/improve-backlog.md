---
description: Improve multiple Jira tasks from a backlog or sprint
allowed-tools: Read, Write, WebFetch, WebSearch
argument-hint: "[JQL query or backlog description]"
---

Improve multiple Jira tasks from a backlog or sprint: $ARGUMENTS

**Entry point:** `/improve-backlog`

Invoke the `pm-task-writer` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-task-writer/SKILL.md` in bulk mode with the provided arguments (JQL query, natural language description, or empty).
