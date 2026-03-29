# insider-pm-plugin-marketplace

Insider team's shared plugin marketplace for Claude Code.

## Available Plugins

| Plugin | Category | Description |
|--------|----------|-------------|
| [warehouse-guide](./warehouse-guide/) | Development | Expert assistant for building Go integrations with Snowflake, BigQuery, Databricks, and Redshift |
| [insider-competitor-intel](./insider-competitor-intel/) | Productivity | Competitive intelligence and benchmarking tool for Insider One — covering Braze, MoEngage, Bloomreach, Klaviyo, Hightouch, Census, Salesforce MC, CleverTap, and Iterable |
| [insider-pm-knowledge-hub](./insider-pm-knowledge-hub/) | Productivity | Unified product knowledge assistant for Insider PMs — searches Confluence, Jira, codebase, Insider Academy, and Slack with Hop validation |
| [insider-pm-doc-writer](./insider-pm-doc-writer/) | Productivity | Team-customizable documentation writer for Insider PMs — generates and publishes Confluence docs using context from all knowledge sources |
| [insider-pm-pvd-writer](./insider-pm-pvd-writer/) | Productivity | PVD creator for Insider PMs — competitive research, cross-team alignment, and full knowledge source context |

## Setup

### 1. Add the marketplace

```bash
claude plugin marketplace add <org>/insider-pm-plugin-marketplace
```

Replace `<org>` with the GitHub org or user where this repo is hosted (e.g. `InsiderEng/insider-pm-plugin-marketplace`).

### 2. Install a plugin

```bash
claude plugin install warehouse-guide@insider-pm-plugin-marketplace
```

### 3. Reload plugins

Inside a Claude Code session, run `/reload-plugins` to activate newly installed plugins.

## Contributing a New Plugin

1. Create a new directory under this repo with your plugin name (e.g. `my-plugin/`).
2. Add the required structure:
   ```
   my-plugin/
   ├── .claude-plugin/
   │   └── plugin.json        # Plugin manifest (name, version, description)
   ├── commands/               # Slash commands (optional)
   │   └── my-command.md
   ├── skills/                 # Skills (optional)
   │   └── my-skill/
   │       └── SKILL.md
   └── README.md               # Document what your plugin does
   ```
3. Register your plugin in `.claude-plugin/marketplace.json` by adding an entry to the `plugins` array.
4. Validate your plugin:
   ```bash
   claude plugin validate ./my-plugin
   ```
5. Open a PR for review.

## Updating Plugins

After pulling new changes or when plugins are updated upstream:

```bash
claude plugin marketplace update insider-pm-plugin-marketplace
claude plugin update <plugin-name>@insider-pm-plugin-marketplace
```
