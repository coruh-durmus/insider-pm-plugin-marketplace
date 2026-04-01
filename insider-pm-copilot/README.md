# insider-pm-copilot

All-in-one PM copilot for Insider — ask product questions, research competitors, improve Jira tasks, write documentation, and create PVDs.

## Requirements

- **ekb MCP** — Insider's internal knowledge base (Jira, Confluence, codebase search)
- **Slack MCP** — Slack read/search and Hop bot messaging
- **Web access** — WebSearch and WebFetch for Academy and live documentation

Optional:
- **Atlassian MCP** (authenticated) — for direct Confluence writes. If not available, doc/PVD writing falls back to draft mode.

## Getting Started

1. Install: `claude plugin install insider-pm-copilot@insider-pm-plugin-marketplace`
2. Run `/setup` to configure for your team
3. Start using commands

## Commands

| Capability | Command | Description |
|------------|---------|-------------|
| **Knowledge** | `/product-search <query>` | Search all sources equally |
| | `/find-spec <query>` | Find specs — Confluence first |
| | `/academy-learn <query>` | Search Academy guides |
| | `/ticket-context <query>` | Find Jira tickets — ekb first |
| | `/code-insight <query>` | Search codebase — ekb first |
| **Competitive Intel** | `/benchmark <competitor> [focus]` | Benchmark against Insider One |
| | `/competitive-brief <name(s)\|all> [area]` | Generate competitive brief |
| | `/feature-compare <feature> [competitor\|all]` | Compare a feature across competitors |
| | `/release-tracker <name\|all> [days]` | Track recent competitor releases |
| **Task Improvement** | `/improve-task [ticket-key]` | Improve a single task description |
| | `/improve-backlog [JQL]` | Improve multiple tasks from a backlog |
| **Documentation** | `/write-docs [ticket-key]` | Create or update Confluence docs |
| **PVD** | `/create-pvd` | Create a Product Value Document |
| **Setup** | `/setup` | Configure the plugin for your team |

## Skills

| Skill | Triggers on |
|-------|-------------|
| `pm-internal-knowledge` | Product questions, spec searches, code insights |
| `competitor-intelligence` | Competitor benchmarks, comparisons, briefs |
| `pm-task-writer` | Task improvement, backlog scoring |
| `pm-doc-writer` | Documentation creation/updates |
| `pm-pvd-writer` | PVD creation |

## Agents

| Agent | Role | Triggered by |
|-------|------|-------------|
| `competitor-researcher` | Autonomous deep-dive on a single competitor | Deep-dive requests, dispatched by orchestrator |
| `competitive-brief-orchestrator` | Parallel brief generation for 3+ competitors | `/competitive-brief` with multiple competitors |
| `knowledge-researcher` | Deep multi-source internal research | Deep internal research requests |
| `backlog-improver` | Orchestrates parallel bulk task improvement | `/improve-backlog` with parallel mode |
| `task-improver` | Improves a single task (worker) | Dispatched by backlog-improver |

## Configuration

Run `/setup` to create `config/team-config.json`. The config has four sections:

```json
{
  "team_name": "YourTeam",
  "docs": { "confluence_space": "...", "confluence_parent_page_id": "...", ... },
  "pvd": { "pvd_parent_page_id": "..." },
  "tasks": { "additional_sources": [...] }
}
```

- **Knowledge** and **Competitive Intel** need no config — they work immediately after install
- **docs**, **pvd**, and **tasks** sections can be configured independently
- Re-run `/setup` anytime to add or change sections

## Permissions

| Source | Read | Write |
|--------|------|-------|
| Confluence | Via ekb MCP | Only with double confirmation + Atlassian MCP |
| Jira | Via ekb MCP | Never |
| Codebase | Via ekb MCP | Never |
| Academy | Via WebSearch/WebFetch | N/A |
| Slack | Via Slack MCP | Only DMs to @hop |
| Local files | Yes | Only `task-improvements/` folder |

## Covered Competitors

Braze, MoEngage, Bloomreach, Klaviyo, Hightouch, Census (Fivetran Activations), Salesforce Marketing Cloud, CleverTap, Iterable.
