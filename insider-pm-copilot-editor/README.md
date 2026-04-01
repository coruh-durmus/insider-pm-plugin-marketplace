# insider-pm-copilot-editor

Copilot lifecycle editor for Insider PMs — add, modify, and remove skills, commands, agents, and competitors in `insider-pm-copilot`. Also creates standalone plugins for the marketplace.

## Requirements

- **Recommended:** `skill-creator` plugin (for generating and testing new skills)
- **insider-pm-copilot** must be installed as a sibling plugin

## Getting Started

```bash
claude plugin install insider-pm-copilot-editor@insider-pm-plugin-marketplace
```

Then run:

```
/edit-copilot
```

## Commands

| Command | Description |
|---------|-------------|
| `/edit-copilot [action] [type]` | Add, modify, remove, or view components in the PM copilot |
| `/create-plugin` | Create a new standalone plugin through a guided interview |

## How It Works — Copilot Editor

1. **Scans copilot state** — discovers all skills, commands, agents, competitor references, and config sections
2. **Choose action** — add, modify, remove, or view
3. **Guided workflow** — interview for add, inline editing for modify, dependency scan + double confirm for remove
4. **Updates related files** — README, config, setup wizard automatically updated after every change
5. **Review and publish** — approve changes, then commit, push, or leave uncommitted

### Components It Can Manage

| Component | Add | Modify | Remove |
|-----------|-----|--------|--------|
| Skills (with commands) | Interview + skill-creator | Edit SKILL.md + update dependents | Dependency scan + cleanup |
| Commands | Pick skill + describe | Edit command file | Remove + clean references |
| Agents | Interview (worker/orchestrator) | Edit agent file | Remove + clean references |
| Competitors | Research via web + generate reference | Edit reference file + roster | Remove from references + roster |
| Config sections | Describe fields | Edit example + setup wizard | Remove from example + wizard |

## How It Works — Plugin Creator

For standalone plugins (outside the copilot):

1. **Choose starting point** — from scratch or adapt an existing plugin
2. **Interview** — 7 questions about purpose, name, commands, config, data sources, write operations
3. **Auto-detect dependencies** — suggests `insider-pm-copilot`, `warehouse-guide`, or `prismatic-guide` based on needs
4. **MCP guidance** — helps set up MCP servers for custom data sources
5. **Generate** — creates full plugin structure using `skill-creator` for skills
6. **Review** — shows each file for approval
7. **Publish** — adds to marketplace or outputs to a separate folder

## Existing Plugins It Knows About

| Plugin | What it provides |
|---|---|
| insider-pm-copilot | All-in-one PM copilot (knowledge, competitive intel, tasks, docs, PVD) |
| warehouse-guide | Snowflake, BigQuery, Databricks, Redshift expertise |
| prismatic-guide | Prismatic.io integration expertise |

## Marketplace

https://github.com/Corcit/insider-pm-plugin-marketplace
