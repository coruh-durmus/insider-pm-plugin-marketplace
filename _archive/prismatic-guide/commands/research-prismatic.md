---
description: Research Prismatic.io platform capabilities for planning integrations
allowed-tools: WebSearch, WebFetch
argument-hint: "[capability or use case to research]"
---

The user wants to research Prismatic.io capabilities to inform integration planning. Their request: $ARGUMENTS

Follow these steps:

1. Clarify the scope: what integration capability or use case they're investigating. If not specified, ask for clarification.

2. Search the official Prismatic.io documentation for current, accurate information. Prismatic releases features frequently — always check the latest docs rather than relying on training knowledge alone. Key starting points:
   - Docs home: https://prismatic.io/docs/
   - Custom components: https://prismatic.io/docs/custom-components/writing-custom-components/
   - Code-native: https://prismatic.io/docs/code-native/
   - Embedded: https://prismatic.io/docs/embed/
   - API: https://prismatic.io/docs/api/api-overview/
   - Connectors: https://prismatic.io/docs/components/component-catalog/
   - Connections: https://prismatic.io/docs/connections/

3. Provide a thorough assessment:
   - Whether Prismatic.io supports the capability and how
   - Which approach is best suited: pre-built connector, custom component, or code-native integration
   - Any relevant limits, quotas, or constraints
   - TypeScript SDK support status (is it exposed via `@prismatic-io/spectral`?)
   - Links to the relevant documentation

4. If multiple approaches exist, present a comparison:
   | Approach | Pros | Cons | Best For |
   |----------|------|------|----------|
   | Pre-built connector | ... | ... | ... |
   | Custom component | ... | ... | ... |
   | Code-native | ... | ... | ... |

5. Add a **Recommendation** section that summarizes:
   - Which approach best fits the use case and why
   - Any tradeoffs or gaps to be aware of
   - Suggested next steps (proof-of-concept, further reading, etc.)

6. Note any features that are in beta or recently launched where behavior may still change, and flag them explicitly.

Focus on actionable insights that help the team decide how to build the integration.
