---
description: Research platform capabilities for new feature development
allowed-tools: WebSearch, WebFetch
argument-hint: [feature area or capability to research]
---

The user wants to research data platform capabilities to inform new feature development. Their request: $ARGUMENTS

Follow these steps:

1. Clarify the scope: which platform(s) to research and what capability or feature area they're investigating. If not specified, research all four platforms (Snowflake, BigQuery, Databricks, Redshift).

2. Search the official documentation for each relevant platform to get current, accurate information. These platforms release features frequently — always check the latest docs rather than relying on training knowledge alone.

3. For each platform, provide:
   - Whether the feature/capability exists and its current maturity (GA, Preview, Beta)
   - How it works at a high level
   - Any relevant limits, quotas, or pricing considerations
   - Go SDK/driver support status (is it exposed via the Go client?)
   - Links to the relevant documentation

4. Present findings as a comparison across platforms where applicable. Use a table for side-by-side comparisons of availability and key differences.

5. Add a **Recommendation** section that summarizes:
   - Which platform(s) best support the use case and why
   - Any tradeoffs or gaps to be aware of
   - Suggested next steps (proof-of-concept, further reading, etc.)

6. Note any features that are in preview or recently launched where behavior may still change, and flag them explicitly.

Focus on actionable insights that help the team decide which platform to use and how to build the integration.
