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

The installer sets up 4 plugins:

| Plugin | Config |
|---|---|
| insider-pm-copilot | Docs: ProductKB/3481600323, PVD: 4087152775, Tasks: warehouse-guide + Shopify + prismatic |
| warehouse-guide | No config needed |
| prismatic-guide | No config needed |
| insider-pm-copilot-editor | No config needed |

## How It Works

1. Installs each plugin from the marketplace in order
2. Shows the pre-configured Kraken settings for each plugin
3. Waits for your confirmation before moving to the next
4. Writes the config files automatically

After installation, run `/reload-plugins` to activate all plugins.

## Customizing After Install

All configs are pre-set for the Kraken team. To change any setting, run `/setup` to reconfigure the copilot.

## Creating Your Own Team Installer

Want to create an installer for your team? Use `/create-plugin` and describe that you want a team installer. Or use this plugin as a reference.
