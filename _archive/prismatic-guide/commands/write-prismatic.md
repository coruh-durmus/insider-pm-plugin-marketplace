---
description: Write TypeScript code for Prismatic.io custom components, integrations, or API scripts
allowed-tools: WebSearch, WebFetch
argument-hint: "[what to build — custom component, integration, embedded setup, API script]"
---

The user wants to write code for a Prismatic.io integration. Their request: $ARGUMENTS

Follow these steps:

1. Identify what to build:
   - **Custom component**: A reusable component with actions, triggers, connections, and/or data sources
   - **Code-native integration**: A full integration defined programmatically in TypeScript
   - **Embedded setup code**: JavaScript/TypeScript code to embed the Prismatic marketplace or designer
   - **API/CLI script**: GraphQL queries or prism CLI commands for managing resources

2. If the task involves a specific SDK feature, API method, or configuration option that may have changed recently, search the official docs before writing code:
   - Spectral SDK: https://prismatic.io/docs/custom-components/spectral/
   - Code-native: https://prismatic.io/docs/code-native/
   - Embedded SDK: https://prismatic.io/docs/embed/embedded-sdk/
   - GraphQL API: https://prismatic.io/docs/api/api-overview/

3. Write complete, production-quality TypeScript code that:
   - Uses the correct Prismatic.io packages (`@prismatic-io/spectral` for components/integrations, `@prismatic-io/embedded` for embedded)
   - Defines proper input types with labels, descriptions, and validation
   - Implements connection definitions with correct auth types (OAuth 2.0, API key, basic, etc.)
   - Uses config variables for customer-specific settings
   - Implements proper error handling with clear messages
   - Follows Prismatic.io patterns (action definitions, trigger handlers, data source resolvers)
   - Uses TypeScript types from Spectral (ActionPerformFunction, TriggerPerformFunction, etc.)

4. Structure the code with:
   - A brief comment block explaining what the component/integration does
   - Clear action/trigger/connection naming that follows Prismatic conventions
   - Inline comments for non-obvious platform-specific behavior

5. After the code, provide:
   - Required npm dependencies (`npm install @prismatic-io/spectral` etc.)
   - Prism CLI commands for building, publishing, and deploying:
     - `prism components:publish` for custom components
     - `prism integrations:import` for code-native integrations
   - Any platform-side setup needed (OAuth app registration, webhook URLs, connection configuration)
   - Notes on testing: how to test locally with `prism components:dev` or integration test runners

6. If there are multiple valid approaches (e.g., custom component vs code-native, low-code vs full-code), briefly present the tradeoffs and explain which is used and why.
