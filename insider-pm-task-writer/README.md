# Insider PM Task Writer

A Claude Code plugin that helps Insider PMs improve Jira task descriptions. Scores task quality, gathers context from all company knowledge sources, and generates improved descriptions with proper user stories, acceptance criteria, context, and edge cases. Works on single tasks or entire backlogs/sprints. Outputs copy-ready markdown files — never writes to Jira.

## Requirements

- **`insider-pm-knowledge-hub` plugin** — must be installed first (all internal context gathering)
- **`insider-competitor-intel` plugin** — must be installed first (competitive research)

## Getting Started

### 1. Install dependencies

```bash
claude plugin install insider-pm-knowledge-hub@insider-pm-plugin-marketplace
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

The wizard will verify your knowledge hub is configured. Additional data sources (e.g., Shopify MCP, warehouse-guide) are configured centrally in the knowledge hub via `/setup-knowledge-hub` — this plugin inherits them automatically.

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

Additional data sources (e.g., warehouse-guide, Shopify MCP) are configured centrally in the knowledge hub via `/setup-knowledge-hub`. This plugin inherits them automatically through keyword-based routing. To add or change additional sources, run `/setup-knowledge-hub`.

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
