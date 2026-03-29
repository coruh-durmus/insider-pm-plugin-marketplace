# Insider PM Plugin Creator

A Claude Code plugin that helps Insider PMs create new plugins for the marketplace. Describe what you need in plain language, and the plugin generates the full structure — plugin.json, commands, skills, config, README — following established patterns. No technical knowledge required.

## Requirements

- **`skill-creator` plugin** — used to generate and refine skills (should be installed from the official Claude Code marketplace)

## Getting Started

```bash
claude plugin install insider-pm-plugin-creator@insider-pm-plugin-marketplace
```

Then run:

```
/create-plugin
```

## Commands

| Command | Description |
|---|---|
| `/create-plugin` | Create a new plugin through a guided interview |

## How It Works

1. **Choose starting point** — start from scratch or adapt an existing plugin
2. **Interview** — answer questions about what the plugin should do, its name, commands, and data sources
3. **Auto-detect dependencies** — the plugin recommends which existing Insider plugins to depend on
4. **MCP guidance** — if you need a new data source, the plugin guides you through setting up an MCP server
5. **Generate** — creates the full plugin structure, using skill-creator to build and test each skill
6. **Review** — shows each generated file for your approval
7. **Publish** — add to the marketplace repo, or output to a separate folder. Optionally open a PR.

## What It Generates

A complete plugin with:
- `plugin.json` — metadata and dependencies
- Commands — setup wizard + action commands
- Skills — generated and tested via skill-creator
- Config — example config for team customization (if needed)
- README — documentation for other PMs

## Existing Plugins It Knows About

The plugin-creator reads the marketplace to understand available plugins and suggest dependencies:

| Plugin | What it provides |
|---|---|
| insider-pm-knowledge-hub | Context from Confluence, Jira, codebase, Academy, Slack, Hop |
| insider-competitor-intel | Competitive research and benchmarking |
| warehouse-guide | Snowflake, BigQuery, Databricks, Redshift expertise |
| insider-pm-doc-writer | Confluence documentation generation |
| insider-pm-pvd-writer | Product Value Document creation |
| insider-pm-task-writer | Jira task description improvement |

## Marketplace

New plugins can be submitted via PR to:
https://github.com/Corcit/insider-pm-plugin-marketplace

The plugin can open the PR for you automatically.
