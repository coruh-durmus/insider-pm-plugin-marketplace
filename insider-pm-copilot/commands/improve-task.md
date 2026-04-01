---
description: Improve a single Jira task description with user story and acceptance criteria
allowed-tools: Read, Write, WebFetch, WebSearch
argument-hint: "[ticket-key]"
---

Improve a single Jira task description: $ARGUMENTS

**Entry point:** `/improve-task`

Invoke the `pm-task-writer` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-task-writer/SKILL.md` in single-task mode with the provided arguments (ticket key, pasted description, or empty).
