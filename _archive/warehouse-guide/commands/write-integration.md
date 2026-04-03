---
description: Write a Go integration for a data platform
allowed-tools: WebSearch, WebFetch
argument-hint: "[platform] [what the integration should do]"
---

The user wants to write a Go integration for a data platform. Their request: $ARGUMENTS

Follow these steps:

1. Identify the target platform (Snowflake, BigQuery, Databricks, Redshift) and clarify the integration goal if not clear (e.g., read data, write data, run a job, bulk load, stream, etc.).

2. If the task involves a specific SDK feature, API method, or configuration option that may have changed recently, search the official docs before writing code.

3. Write complete, production-quality Go code that:
   - Uses the correct Go driver or SDK for the platform (see skill references for module paths)
   - Reads credentials from environment variables, never hardcodes them
   - Uses `context.Context` throughout for cancellation and timeout support
   - Implements proper error handling with wrapped errors and clear messages
   - Includes connection pooling where applicable (`sql.DB` settings for MaxOpenConns, etc.)
   - Uses parameterized queries to prevent SQL injection
   - Implements retry with exponential backoff for transient errors where appropriate
   - Follows idiomatic Go patterns (defer cleanup, named return errors, etc.)

4. Structure the code with:
   - A brief comment block explaining what the integration does
   - Clear function/variable naming
   - Inline comments for non-obvious platform-specific behavior

5. After the code, provide:
   - Required Go module dependencies (`go get ...` commands)
   - Required environment variables and how to obtain them
   - Any platform-side setup needed (IAM roles, API keys, network config)
   - Notes on performance, cost, or scaling considerations

6. If there are multiple valid approaches (e.g., Data API vs. direct connection for Redshift), briefly present the tradeoffs and explain which is used and why.
