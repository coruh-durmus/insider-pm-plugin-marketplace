# Insider PM PVD Writer

A Claude Code plugin that helps Insider PMs create Product Value Documents (PVDs) with detailed requirements. The plugin reads Insider's company-wide PVD template from Confluence, gathers context from all internal knowledge sources, performs competitive research, discovers cross-team alignment needs, and walks you through PVD creation with review at every step.

## Requirements

- **`insider-pm-knowledge-hub` plugin** — must be installed first (all internal context gathering)
- **`insider-competitor-intel` plugin** — must be installed first (competitive research)
- **Atlassian MCP** — for Confluence read and write access

## Getting Started

### 1. Install dependencies

```bash
claude plugin install insider-pm-knowledge-hub@insider-pm-plugin-marketplace
claude plugin install insider-competitor-intel@insider-pm-plugin-marketplace
```

### 2. Install this plugin

```bash
claude plugin install insider-pm-pvd-writer@insider-pm-plugin-marketplace
```

### 3. Run the setup wizard

```
/setup-pvd-plugin
```

The wizard will ask for your team name and PVD folder location in Confluence.

## Commands

| Command | Description |
|---|---|
| `/setup-pvd-plugin` | Configure the plugin for your team (team name, PVD parent page) |
| `/create-pvd` | Create a new Product Value Document |

### Usage Examples

```
/create-pvd                   # Describe a feature and create a PVD
```

When creating a PVD, you can optionally reference an existing PVD as an example for style and depth.

## How It Works

1. **Input** — describe the feature/product and optionally reference an existing PVD
2. **Gather context** — searches all knowledge sources (Confluence, Jira, codebase, Academy, Slack, Hop) for relevant information
3. **Competitor research** — analyzes how competitors handle the same feature area
4. **Cross-team alignment** — discovers which teams need to be involved and asks you to confirm
5. **Read template** — reads the company-wide PVD template from Confluence
6. **Draft** — you choose section-by-section or full draft mode, with inline examples supported
7. **Review** — shows content for your approval before any action
8. **Publish** — write directly to Confluence (with double confirmation) or output as draft

## Configuration

After running `/setup-pvd-plugin`, your config is saved to `config/team-config.json` (git-ignored). To reconfigure, run `/setup-pvd-plugin` again.

See `config/team-config.json.example` for the expected format.

## Permissions

| Source | Access | How |
|---|---|---|
| All knowledge sources | Read-only | Via `insider-pm-knowledge-hub` |
| Competitor research | Read-only | Via `insider-competitor-intel` |
| Confluence | Write | Only with your explicit approval + double confirmation |

The plugin **never writes to Confluence automatically**. Every write requires you to:
1. Choose "Write directly to Confluence" (vs. draft mode)
2. Confirm with "yes" when asked "Are you sure?"
