---
description: Search all company knowledge sources for product information
allowed-tools: WebFetch, WebSearch, Read, mcp__ekb__jira, mcp__ekb__semantic_search, mcp__ekb__symbol_search, mcp__ekb__refs, mcp__ekb__file, mcp__ekb__tree
argument-hint: "<product question>"
---

Search all knowledge sources for: $ARGUMENTS

**Source priority:** All sources equally weighted.

**Entry point:** `/product-search`

Invoke the `pm-internal-knowledge` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-internal-knowledge/SKILL.md` with the user's query. Follow all steps in the skill, using "All sources equally" as the source priority.
