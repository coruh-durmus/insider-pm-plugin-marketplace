# Insider Kraken Plugin Installer

One-command installer that sets up all Insider PM plugins with Kraken team configurations. New Kraken PMs can run a single command to get their entire plugin environment ready.

## Getting Started

```bash
claude plugin install insider-kraken-plugin-installer@insider-pm-plugin-marketplace
```

Then run:

```
/install-kraken-plugins
```

## What It Installs

The installer sets up 7 plugins in dependency order:

| Plugin | Config |
|---|---|
| insider-pm-internal-knowledge | No config needed |
| insider-competitor-intel | No config needed |
| warehouse-guide | No config needed |
| insider-pm-doc-writer | Confluence: ProductKB, Parent Page: 3481600323, Jira: SD |
| insider-pm-pvd-writer | PVD Parent Page: 4087152775 |
| insider-pm-task-writer | Sources: warehouse-guide, Shopify MCP, prismatic-guide |
| insider-pm-plugin-creator | No config needed |

## How It Works

1. Installs each plugin from the marketplace in dependency order
2. Shows the pre-configured Kraken settings for each plugin
3. Waits for your confirmation before moving to the next
4. Writes the config files automatically

After installation, run `/reload-plugins` to activate all plugins.

## Customizing After Install

All configs are pre-set for the Kraken team. If you need to change any setting, run the individual plugin's setup wizard:

- `/setup-docs` — reconfigure doc-writer
- `/setup-pvd-plugin` — reconfigure PVD writer
- `/setup-task-writer` — reconfigure task writer

## Creating Your Own Team Installer

Want to create an installer for your team? Use `/create-plugin` and describe that you want a team installer. Or use this plugin as a reference.
