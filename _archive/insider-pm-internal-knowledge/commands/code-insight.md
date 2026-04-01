---
description: Understand how a feature is implemented in the codebase
allowed-tools: Read, mcp__ekb__semantic_search, mcp__ekb__symbol_search, mcp__ekb__refs, mcp__ekb__file, mcp__ekb__tree, mcp__ekb__jira
argument-hint: "<feature or component>"
---

Explore how this is implemented in the codebase: $ARGUMENTS

**Source priority:** Codebase first (via ekb), then Jira and Confluence.

**Entry point:** `/code-insight`

Invoke the `pm-internal-knowledge` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-internal-knowledge/SKILL.md` with the user's query. Follow all steps in the skill, using "Codebase first" as the source priority.
