---
description: Troubleshoot a data platform integration error
allowed-tools: WebSearch, WebFetch
argument-hint: "[platform] [error message or description of the problem]"
---

The user is troubleshooting an error or unexpected behavior in a data platform integration. Their report: $ARGUMENTS

Follow these steps:

1. Identify the platform (Snowflake, BigQuery, Databricks, Redshift) and the nature of the problem. Ask for clarification if the error message or context is missing.

2. Analyze the error:
   - Parse the error message for the specific error code or type
   - Identify if this is a connection issue, authentication issue, permission error, query error, rate limit, or logic bug

3. If the error code or message is platform-specific and may have changed or expanded recently, search the official docs for the latest explanation and resolution guidance.

4. Provide a structured diagnosis:
   - **Root cause**: What is actually going wrong
   - **Most likely fix**: The step most likely to resolve it
   - **Alternative causes**: Other things to check if the primary fix doesn't work
   - **Prevention**: How to avoid this in the future

5. If code is involved, show corrected Go code with the fix applied and explain what changed.

6. For common error categories, check:
   - **Authentication/permissions**: Token expiry, missing IAM roles, wrong service account, network policies
   - **Connection issues**: VPC config, firewall rules, SSL requirements, connection pool exhaustion
   - **Query errors**: SQL dialect differences, schema mismatches, type casting issues, missing tables
   - **Rate limits / quotas**: Concurrent query limits, API rate limits, slot exhaustion, credit limits
   - **Data issues**: Type mismatches on insert, encoding issues, NULL handling, data size limits

7. Link to the relevant troubleshooting or error reference page in the official docs.
