# Insider PM Task Writer

A Claude Code plugin that helps Insider PMs improve Jira task descriptions. Scores task quality, gathers context from all company knowledge sources, and generates improved descriptions with proper user stories, acceptance criteria, context, and edge cases. Works on single tasks or entire backlogs/sprints. Outputs copy-ready markdown files — never writes to Jira.

## Requirements

- **`insider-pm-internal-knowledge` plugin** — must be installed first (all internal context gathering)
- **`insider-competitor-intel` plugin** — must be installed first (competitive research)

## Getting Started

### 1. Install dependencies

```bash
claude plugin install insider-pm-internal-knowledge@insider-pm-plugin-marketplace
claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
```

### 2. Install this plugin

```bash
claude plugin install insider-pm-task-writer@insider-pm-plugin-marketplace
```

### 3. Run the setup wizard

```
/setup-task-writer
```

The wizard will ask for your team name and any team-specific sources (e.g., Shopify MCP, warehouse-guide).

## Commands

| Command | Description |
|---|---|
| `/setup-task-writer` | Configure the plugin for your team |
| `/improve-task [ticket-key]` | Improve a single task (ticket key or paste description) |
| `/improve-backlog [JQL or description]` | Improve multiple tasks from a backlog or sprint |

### Usage Examples

```
/improve-task SD-1234                    # Improve a specific ticket
/improve-task                            # Paste a description to improve
/improve-backlog project = SD AND sprint = "Sprint 42"   # JQL query
/improve-backlog all todo tasks in Sprint 42              # Natural language
```

## Agents

For bulk processing, the plugin includes two agents that enable parallel task improvement:

| Agent | Role | Description |
|---|---|---|
| `backlog-improver` | Orchestrator | Manages end-to-end bulk workflow: fetch tasks, score, get PM approval, dispatch parallel workers, write summary |
| `task-improver` | Worker | Improves a single task: gathers context from all sources, generates improved description, writes output file |

The orchestrator dispatches up to 5 workers simultaneously, processing large backlogs significantly faster than sequential mode. Select the **Parallel** review mode when prompted during `/improve-backlog` to use agent-accelerated processing.

## Quality Scoring

Tasks are scored on 5 criteria (max 10 points):

| Criteria | What it checks |
|---|---|
| User Story | "As a [role], I want [action], so that [benefit]" |
| Acceptance Criteria | Clear, testable, specific |
| Context | Sufficient background for a developer |
| Edge Cases | Error scenarios and boundary conditions |
| Scope | Well-defined, appropriately sized |

Default threshold: **7/10**. Tasks scoring below this are flagged for improvement. You can override per invocation.

## Additional Sources

During setup, you can configure team-specific sources that the plugin uses based on keyword matching. For example:

- **warehouse-guide** — triggered by keywords: snowflake, bigquery, databricks, redshift
- **Shopify MCP** — triggered by keywords: shopify, shopify webhook

If you need a source that doesn't exist yet, create your own plugin or configure an MCP server for it.

## Output

Improved descriptions are saved as markdown files in the current working directory:

```
task-improvements/
└── YYYY-MM-DD-HH-MM/
    ├── SD-1234.md          # Improved task with original vs. new
    ├── SD-1235.md
    └── summary.md          # Scoring summary (bulk mode)
```

Copy the "Improved Description" section from each file to the corresponding Jira ticket.

## Permissions

| Source | Access |
|---|---|
| All knowledge sources | Read-only (via knowledge hub) |
| Competitor research | Read-only (via competitor intel) |
| Additional sources | Read-only (via configured plugins/MCPs) |
| Jira | **Never writes** |
| Local filesystem | Writes to `task-improvements/` only |
