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
| [insider-pm-task-writer](./insider-pm-task-writer/) | Productivity | Jira task improver — quality scoring, context gathering, and improved descriptions with user stories and AC |
| [insider-pm-plugin-creator](./insider-pm-plugin-creator/) | Productivity | Plugin creation tool — guided interview to generate full plugins with skills, commands, and marketplace integration |

## Setup

### 1. Add the marketplace

```bash
claude plugin marketplace add Corcit/insider-pm-plugin-marketplace
```

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

## Improving Existing Plugins

We encourage everyone to improve the plugins in this marketplace. If you find a bug, want to add a feature, or see a way to make a plugin better — open a PR.

### How to Submit an Improvement

1. Clone the repo and create a feature branch:
   ```bash
   git clone https://github.com/Corcit/insider-pm-plugin-marketplace.git
   cd insider-pm-plugin-marketplace
   git checkout -b fix/<plugin-name>-<short-description>
   ```
2. Make your changes to the plugin files.
3. Open a PR with the following information:
   - **Which plugin** you're improving
   - **What changed** — summary of the improvement
   - **Why** — what problem it solves or what it makes better
   - **Testing** — how you verified the change works

### Guidelines

- **Don't break existing commands.** If a plugin has `/write-docs`, it should still work the same way after your change. Additive changes are preferred over breaking changes.
- **Update the plugin's README** if your change affects behavior, commands, or setup flow.
- **Keep the plugin's conventions.** Read the existing code and follow the same patterns (frontmatter format, section naming, tone).
- **One plugin per PR.** If you're improving multiple plugins, open separate PRs for each.
- **Bump the version** in `plugin.json` if your change is significant (e.g., `0.1.0` → `0.2.0` for new features, `0.1.0` → `0.1.1` for fixes).

### Review & Approval

The PM or team who created the plugin is responsible for reviewing and approving changes to their plugin. Tag the plugin owner in your PR for review:

| Plugin | Owner |
|--------|-------|
| warehouse-guide | @Corcit |
| insider-competitor-intel | Insider PM team |
| insider-pm-knowledge-hub | Insider PM team |
| insider-pm-doc-writer | Insider PM team |
| insider-pm-pvd-writer | Insider PM team |
| insider-pm-task-writer | Insider PM team |
| insider-pm-plugin-creator | Insider PM team |

## Updating Plugins

After pulling new changes or when plugins are updated upstream:

```bash
claude plugin marketplace update insider-pm-plugin-marketplace
claude plugin update <plugin-name>@insider-pm-plugin-marketplace
```
