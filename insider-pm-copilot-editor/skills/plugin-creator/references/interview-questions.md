# Phase 2 — Interview Questions

Ask the PM these questions one at a time. If adapting an existing plugin, pre-fill answers from the base and ask what to change.

## Question 1: Purpose

> "What should this plugin do? Describe it in a few sentences."

## Question 2: Name

Based on the description, suggest a name following the `insider-pm-*` convention:
> "Based on your description, I'd suggest calling it `insider-pm-<suggested-name>`. Does that work, or do you have a different name in mind?"

## Question 3: Commands

Based on the description, suggest commands:
> "Here are the commands I'd suggest:
> - `/setup-<name>` — configure the plugin for your team
> - `/<action-name>` — [main action description]
>
> Does this look right? Add, remove, or rename any?"

## Question 4: Setup Wizard

> "Does this plugin need a setup wizard for team customization?
> For example, to configure:
> - Team name
> - Confluence space or parent page
> - Jira project keys
> - Additional data sources
>
> - A) Yes, it needs a setup wizard
> - B) No, it works the same for everyone"

## Question 5: Data Sources

> "What data sources does this plugin need?
> For example:
> - Product knowledge (Confluence, Jira, codebase, Academy, Slack)
> - Competitive research
> - Specific APIs or platforms
> - None — it works without external data"

## Question 6: Write Operations

> "Should this plugin write to any external system?
> - A) Read-only everywhere
> - B) Can write to Confluence with PM approval
> - C) Outputs local files only
> - D) Other — describe what it should write to"

## Question 7: Anything Else

> "Anything else I should know about how this plugin should work?"
