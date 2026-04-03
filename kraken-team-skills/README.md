# kraken-team-skills

Kraken team-specific skills for Claude Code. Combines data warehouse platform expertise, Prismatic.io embedded iPaaS guidance, and Integration Hub database analytics into a single plugin.

## Skills

| Skill | Description |
|-------|-------------|
| `data-platform-expert` | Snowflake, BigQuery, Databricks, Amazon Redshift — features, Go integrations, troubleshooting |
| `prismatic-expert` | Prismatic.io embedded iPaaS — custom components, code-native integrations, embedded marketplace |
| `integration-hub-analytics` | Query the Integration Hub MySQL database for business analytics on partner integrations |

## Commands

| Command | Description |
|---------|-------------|
| `/setup-kraken` | Configure MySQL credentials for Integration Hub access |
| `/explain-feature` | Explain a data warehouse feature or concept |
| `/research-feature` | Research platform capabilities for new feature development |
| `/troubleshoot` | Troubleshoot a data platform integration error |
| `/write-integration` | Write a Go integration for a data platform |
| `/explain-prismatic` | Explain a Prismatic.io concept, feature, or component |
| `/research-prismatic` | Research Prismatic.io platform capabilities |
| `/troubleshoot-prismatic` | Troubleshoot a Prismatic.io integration error |
| `/write-prismatic` | Write TypeScript code for Prismatic.io components |

## Setup

### Integration Hub Database Access

Each PM needs their own MySQL credentials. Run the setup wizard:

```
/setup-kraken
```

This will:
1. Ask for your MySQL username and password
2. Write a `.env` file in your project root
3. Verify connectivity
4. Show available commands

Alternatively, create a `.env` file manually (see `.env.example`) and run `source .env` before starting Claude Code.

### Environment Variables

| Variable | Description |
|----------|-------------|
| `KRAKEN_MYSQL_USER` | Your MySQL username for the Integration Hub database |
| `KRAKEN_MYSQL_PASSWORD` | Your MySQL password for the Integration Hub database |

## Install

```bash
claude plugin install kraken-team-skills@insider-pm-plugin-marketplace
```
