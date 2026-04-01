---
description: Search Insider Academy for how-to guides and feature explanations
allowed-tools: WebFetch, WebSearch, Read
argument-hint: "<topic to learn about>"
---

Search Insider Academy for learning materials about: $ARGUMENTS

**Source priority:** Academy first (site:academy.insiderone.com), then Confluence.

**Entry point:** `/academy-learn`

Invoke the `pm-internal-knowledge` skill from `${CLAUDE_PLUGIN_ROOT}/skills/pm-internal-knowledge/SKILL.md` with the user's query. Follow all steps in the skill, using "Academy first" as the source priority.
