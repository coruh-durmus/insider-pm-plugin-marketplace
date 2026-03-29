# prismatic-guide

Expert assistant for building embedded iPaaS integrations with [Prismatic.io](https://prismatic.io).

## What it does

Helps Insider teams explain features, write custom components and code-native integrations, troubleshoot errors, and research Prismatic.io platform capabilities. All documentation is fetched live from prismatic.io/docs to ensure accuracy.

## Commands

| Command | Description |
|---------|-------------|
| `/explain-prismatic [topic]` | Explain a Prismatic.io concept, feature, or component |
| `/research-prismatic [use case]` | Research platform capabilities for planning integrations |
| `/troubleshoot-prismatic [error]` | Diagnose integration errors and issues |
| `/write-prismatic [what to build]` | Write custom components, integrations, or API code |

## Coverage

- **Platform concepts**: Integrations, flows, steps, connections, config variables, triggers, instances
- **Custom components**: TypeScript components with `@prismatic-io/spectral` SDK
- **Code-native integrations**: Full-code approach without the low-code designer
- **Embedded experience**: Marketplace embedding, designer embedding, theme customization
- **API & SDK**: GraphQL API, `prism` CLI, YAML definitions
- **Pre-built connectors**: Marketplace connector catalog and configuration

## Installation

```
claude plugin install prismatic-guide@insider-pm-plugin-marketplace
```
