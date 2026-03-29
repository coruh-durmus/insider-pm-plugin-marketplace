---
description: Create or update product documentation for a feature
allowed-tools: Read, Write, WebFetch, WebSearch, mcp__ekb__jira, mcp__ekb__semantic_search, mcp__ekb__symbol_search, mcp__ekb__refs, mcp__ekb__file, mcp__ekb__tree
argument-hint: "[ticket-key]"
---

Create or update product documentation. Optionally provide a Jira ticket key: $ARGUMENTS

Invoke the `pm-doc-writer` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-doc-writer/SKILL.md` with the provided arguments (ticket key or empty).
