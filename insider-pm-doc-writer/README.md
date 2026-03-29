# Insider PM Doc Writer

A Claude Code plugin that helps Insider PMs create and update product documentation in Confluence. Each team customizes the plugin via an interactive setup wizard. The plugin learns your documentation structure from existing pages, gathers context from all company knowledge sources, and generates docs with your review and approval.

## Requirements

- **`insider-pm-knowledge-hub` plugin** — must be installed first (the setup wizard checks for this)
- **Atlassian MCP** — for Confluence read and write access
- **ekb MCP** — for Jira tickets and codebase search (via knowledge hub)
- **Slack MCP** — for Slack search and Hop interaction (via knowledge hub)

## Getting Started

### 1. Install the dependency

```bash
claude plugin install insider-pm-knowledge-hub@insider-pm-plugin-marketplace
```

### 2. Install this plugin

```bash
claude plugin install insider-pm-doc-writer@insider-pm-plugin-marketplace
```

### 3. Run the setup wizard

```
/setup-docs
```

The wizard will ask you about your team, Confluence space, Jira projects, and preferences.

## Commands

| Command | Description |
|---|---|
| `/setup-docs` | Interactive setup wizard — configure the plugin for your team |
| `/write-docs [ticket-key]` | Create or update documentation for a feature |

### Usage Examples

```
/write-docs SD-1234          # Document a specific Jira ticket
/write-docs                   # Describe a feature to document (no ticket needed)
```

## How It Works

1. **Gather context** — searches Confluence, Jira, codebase, Academy, Slack, and Hop for information about the feature
2. **Detect new vs. update** — checks if documentation already exists for this feature
3. **Learn structure** — reads your existing docs to learn your team's documentation style
4. **Generate content** — creates documentation matching your team's patterns
5. **Review** — shows the content for your approval before any action
6. **Publish** — you choose: write directly to Confluence or output as a draft to copy yourself

## Configuration

After running `/setup-docs`, your config is saved to `config/team-config.json` (git-ignored). To reconfigure, run `/setup-docs` again.

See `config/team-config.json.example` for the expected format.

## Permissions

| Source | Read | Write |
|---|---|---|
| Confluence | All pages/spaces | Only with your explicit approval + double confirmation |
| Jira | All tickets (via knowledge hub) | Never |
| Codebase | All files (via knowledge hub) | Never |
| Academy | All articles (via knowledge hub) | N/A |
| Slack | All channels and DMs (via knowledge hub) | Only DMs to @hop (via knowledge hub) |

The plugin **never writes to Confluence automatically**. Every write requires you to:
1. Choose "Write directly to Confluence" (vs. draft mode)
2. Confirm with "yes" when asked "Are you sure?"
