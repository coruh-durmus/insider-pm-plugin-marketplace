---
description: Improve multiple Jira tasks from a backlog or sprint
allowed-tools: Read, Write, WebFetch, WebSearch, Agent
argument-hint: "[JQL query or backlog description]"
---

Improve multiple Jira tasks from a backlog or sprint: $ARGUMENTS

**Entry point:** `/improve-backlog`

Invoke the `pm-task-writer` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-task-writer/SKILL.md` in bulk mode with the provided arguments (JQL query, natural language description, or empty).

For parallel bulk processing, dispatch the `backlog-improver` orchestrator agent from `${CLAUDE_PLUGIN_ROOT}/agents/backlog-improver.md`. The orchestrator scores all tasks, gets PM approval, then dispatches parallel `task-improver` workers for simultaneous improvement.
