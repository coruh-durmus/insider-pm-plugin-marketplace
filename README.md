# insider-pm-plugin-marketplace

Insider team's shared plugin marketplace for Claude Code.

## Available Plugins

| Plugin | Category | Description |
|--------|----------|-------------|
| [insider-pm-copilot](./insider-pm-copilot/) | Productivity | All-in-one PM copilot — internal knowledge, competitive intelligence, task improvement, doc writing, and PVD creation |
| [insider-pm-copilot-editor](./insider-pm-copilot-editor/) | Productivity | Copilot lifecycle editor — manage copilot components or create standalone plugins |
| [kraken-team-skills](./kraken-team-skills/) | Team | Kraken team skills — data warehouse platforms, Prismatic.io embedded iPaaS, and Integration Hub database analytics |
| [tech-design-lifecycle](./tech-design-lifecycle/) | Development | End-to-end tech design lifecycle — brainstorming, design generation, iterative review, refinement, and preflight |

## Getting Started

### 1. Add the marketplace

```bash
/plugin marketplace add Corcit/insider-pm-plugin-marketplace
```

Then run `/plugin`, select **insider-pm-plugin-marketplace**, and select **Enable auto-update**.

### 2. Install the copilot

```bash
/plugin install insider-pm-copilot@insider-pm-plugin-marketplace
/reload-plugins
```

### 3. Run setup

```
/setup
```

If your team has a preset (e.g., Kraken), select it and confirm — setup is done in one step. If your team is new, the wizard walks you through configuration and offers to save your setup as a preset for your teammates.

### Installing additional plugins

```bash
/plugin install <plugin-name>@insider-pm-plugin-marketplace
/reload-plugins
```

Available: `kraken-team-skills`, `insider-pm-copilot-editor`, `tech-design-lifecycle`

## Contributing a New Plugin

The easiest way to create a new plugin is to use the **Plugin Creator**:

```
/create-plugin
```

This walks you through a guided interview, generates the full plugin structure (plugin.json, commands, skills, config, README), and can open a PR to this marketplace automatically. No technical knowledge required.

### Manual Approach

If you prefer to create a plugin manually:

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
4. Open a PR for review.

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
| insider-pm-copilot | Insider PM team |
| insider-pm-copilot-editor | Insider PM team |
| kraken-team-skills | Kraken team |
| tech-design-lifecycle | Insider PM team |

## Keeping Plugins Up to Date

Enable auto-update so your plugins stay current automatically:

1. Run `/plugin`
2. Select **insider-pm-plugin-marketplace**
3. Select **Enable auto-update**

That's it — whenever the marketplace is updated with new plugins or improvements, your installed plugins will update automatically.

If you prefer manual updates:

```bash
/plugin marketplace update insider-pm-plugin-marketplace
/reload-plugins
```
