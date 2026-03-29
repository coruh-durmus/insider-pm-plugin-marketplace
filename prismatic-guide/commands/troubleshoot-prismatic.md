---
description: Troubleshoot a Prismatic.io integration error or issue
allowed-tools: WebSearch, WebFetch
argument-hint: "[error message or description of the problem]"
---

The user is troubleshooting an error or unexpected behavior in a Prismatic.io integration. Their report: $ARGUMENTS

Follow these steps:

1. Identify the nature of the problem. Ask for clarification if the error message or context is missing.

2. Categorize the issue:
   - **Custom component error**: Build failure, runtime error in action/trigger, input validation, SDK version mismatch
   - **Deployment issue**: Prism CLI error, publish failure, version conflict, YAML parsing error
   - **Embedded marketplace issue**: Iframe loading, theme/styling, SDK initialization, authentication flow
   - **Connection/auth failure**: OAuth flow error, token refresh, API key invalid, connection test failure
   - **Trigger problem**: Webhook not firing, schedule misconfigured, payload parsing error
   - **API error**: GraphQL query failure, permission denied, rate limit, pagination issue
   - **Instance configuration**: Config variable mismatch, customer-specific settings, flow activation error
   - **Execution error**: Step failure mid-flow, data type mismatch between steps, timeout, retry behavior

3. Search the official Prismatic.io documentation for relevant troubleshooting guidance:
   - Docs home: https://prismatic.io/docs/
   - Custom components: https://prismatic.io/docs/custom-components/writing-custom-components/
   - CLI: https://prismatic.io/docs/cli/cli-usage/
   - Embedded SDK: https://prismatic.io/docs/embed/embedded-sdk/
   - API: https://prismatic.io/docs/api/api-overview/

4. Provide a structured diagnosis:
   - **Root cause**: What is actually going wrong
   - **Most likely fix**: The step most likely to resolve it
   - **Alternative causes**: Other things to check if the primary fix doesn't work
   - **Prevention**: How to avoid this in the future

5. If code is involved, show corrected TypeScript code with the fix applied and explain what changed.

6. For common error categories, check:
   - **Authentication/connections**: OAuth callback URL mismatch, token expiry, missing scopes, connection type mismatch
   - **Custom components**: Spectral SDK version compatibility, input type errors, missing required inputs, action return type
   - **Deployment**: Prism CLI version, component signature conflicts, YAML schema validation
   - **Embedded**: CORS issues, iframe permissions, JWT token generation, theme CSS conflicts
   - **Execution**: Step output shape mismatch, null/undefined propagation, timeout configuration, retry settings
   - **API**: GraphQL schema changes, deprecated fields, authentication header format, query complexity limits

7. Link to the relevant troubleshooting or error reference page in the official docs.
