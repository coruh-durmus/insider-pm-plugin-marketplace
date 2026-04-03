---
description: Explain a feature or concept for a data platform
allowed-tools: WebSearch, WebFetch
argument-hint: "[platform] [feature or concept]"
---

The user wants an explanation of a data platform feature or concept. Their request: $ARGUMENTS

Follow these steps:

1. Identify which platform(s) the question is about (Snowflake, BigQuery, Databricks, Redshift). If not specified, ask.

2. Search the official documentation for up-to-date information on the feature. Use the documentation URLs from the data-platform-expert skill references. Prefer fetching from official docs over relying solely on training knowledge, especially for features that evolve frequently.

3. Provide a clear explanation that covers:
   - What the feature is and what problem it solves
   - How it works conceptually
   - Key configuration options or parameters
   - A practical Go code example if the feature involves programmatic interaction
   - Important limits, quotas, or pricing implications
   - Common gotchas or things to watch out for

4. If the same concept exists across multiple platforms, briefly note how implementations differ.

5. End with links to the relevant official documentation sections for deeper reading.

Keep the explanation practical and focused on what an integration developer needs to know.
