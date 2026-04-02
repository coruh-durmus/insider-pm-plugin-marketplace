---
name: pm-plugin-creator
description: >
  This skill should be used when the user asks to "create a plugin", "build a new plugin",
  "make a plugin for X", "I need a plugin that does Y", "scaffold a plugin",
  "create a marketplace plugin", or any request to create a new Claude Code plugin
  for the Insider PM marketplace.
version: 0.1.0
---

# PM Plugin Creator

Guide PMs through creating new Claude Code plugins via a structured 7-phase workflow. Generate full plugin structures — plugin.json, commands, skills, config, README — following established patterns from the existing Insider marketplace. Use plain, PM-friendly language throughout.

## Permissions

- **Marketplace directory:** Read to understand existing plugins, patterns, and available dependencies
- **`skill-creator` skill:** Invoke to generate, eval, and iterate skills for the new plugin
- **Local filesystem:** Write to create plugin files (either in the marketplace repo or a separate folder)
- **GitHub:** Write only if the PM chooses to open a PR

The plugin creator does not access Confluence, Jira, Slack, or any other external data source directly.

## Workflow Overview

The creation process follows seven phases:

### Phase 1 — Choose Starting Point

Present two options:
- **A) Start from scratch** — proceed to the interview
- **B) Use an existing plugin as a base** — read the marketplace directory, list available plugins with descriptions, let the PM pick one, then read its full structure as context before the interview

### Phase 2 — Interview

Ask the PM seven questions, one at a time. If adapting an existing plugin, pre-fill answers from the base and ask what to change.

For the full list of interview questions, consult **`references/interview-questions.md`**.

### Phase 3 — Auto-Detect Dependencies

Based on the PM's answers, scan existing Insider plugins and recommend dependencies:

| PM describes... | Suggested dependency |
|---|---|
| Needs product knowledge (Confluence/Jira/codebase/Slack) | `insider-pm-copilot` (pm-internal-knowledge skill) |
| Needs competitive research or benchmarking | `insider-pm-copilot` (competitor-intelligence skill) |
| Needs data warehouse context (Snowflake, BigQuery, etc.) | `warehouse-guide` |
| Needs Prismatic.io integration context | `prismatic-guide` |

Present recommendations and let the PM confirm or adjust.

### Phase 4 — MCP Guidance

If the PM needs a data source without an existing plugin or MCP, explain what an MCP server is in PM-friendly language and guide through setup. If the PM declines, note the missing source in the generated README.

### Phase 5 — Generate Plugin

Generate the full plugin structure following established marketplace patterns. Use `skill-creator` for all skills.

For the full generation templates and file specifications, consult **`references/generation-templates.md`**.

### Phase 6 — Review

Show the PM the complete generated plugin:
1. Display the file tree
2. Show each file's content for review, one at a time
3. After each file, ask if it looks right or needs changes
4. Regenerate files as needed until all are approved

### Phase 7 — Publish

Offer two publishing options:
- **A) Add to the marketplace repo** — create files, register in marketplace.json, update README tables
- **B) Output to a separate folder** — generate at a location the PM chooses

For the full publishing workflow including PR creation, consult **`references/publishing-flow.md`**.

## Important Rules

- **PM-friendly language.** Never use technical jargon without explanation.
- **Follow existing patterns.** Generated plugins must match the structure and conventions of existing marketplace plugins.
- **Always use skill-creator.** Every skill goes through skill-creator's generate, eval, iterate loop.
- **One question at a time.** Never overwhelm the PM with multiple questions.
- **Suggest, don't demand.** Suggest names, commands, dependencies — but let the PM decide.
- **PR is optional.** Never push to main without the PM's explicit choice to open a PR.
- **Naming convention.** Default to `insider-pm-*` naming unless the PM specifies otherwise.
- **Check team installer impact.** When creating a new plugin, check if `insider-kraken-plugin-installer` is affected and flag it if so.
- **Marketplace repo:** https://github.com/Corcit/insider-pm-plugin-marketplace

## Additional Resources

### Reference Files

For detailed workflows, consult:
- **`references/interview-questions.md`** — Full Phase 2 interview with all seven questions
- **`references/generation-templates.md`** — Phase 5 file templates and specifications
- **`references/publishing-flow.md`** — Phase 7 publishing workflow with marketplace registration and PR creation
