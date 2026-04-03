---
name: integration-hub-analytics
description: >-
  This skill should be used when the user asks about integration hub data,
  partner integrations, integration statistics, mapping analysis, trigger
  configurations, webhook throttling, or any business analytics question
  about the External Integrations team's MySQL database (medusa-db / integration-hub-db).
  Examples: "how many active integrations", "which partners have the most integrations",
  "show me Shopify integration details", "what triggers are configured for partner X",
  "integration match breakdown by type".
version: 1.0.0
---

# Integration Hub Analytics

Answer business analytics questions about the External Integrations team's Integration Hub database using the `integration-hub-db` MCP server.

## Connection

- **MCP server name:** `integration-hub-db`
- **Database:** `integration-hub-db` (MySQL, read-only)
- **Tools:** Use MCP tools from the `integration-hub-db` server to execute SQL queries

## Database Schema

Read `references/schema.md` for the full schema documentation before writing any queries.

## Query Guidelines

1. **Always read `references/schema.md` first** to understand tables, columns, and relationships before querying.
2. **Read-only access** — only SELECT queries are allowed. Never attempt INSERT, UPDATE, DELETE, or DDL.
3. **Quote the database name** with backticks in queries since it contains a hyphen: `` `integration-hub-db` ``.
4. **Start broad, then drill down** — begin with aggregations and counts, then narrow based on the user's follow-up.
5. **Use JOINs via `integration_id`** — most tables link back to `integrations.id`.
6. **Present results clearly** — use tables, summaries, and highlight key insights. The user is a PM, not a DBA.
7. **Interpret the data** — don't just return raw results. Provide business context: trends, anomalies, comparisons.

## Common Query Patterns

### Integration overview
```sql
SELECT status, COUNT(*) as count FROM integrations GROUP BY status ORDER BY count DESC;
```

### Partner integration count
```sql
SELECT partner, COUNT(*) as integration_count FROM integrations GROUP BY partner ORDER BY integration_count DESC LIMIT 20;
```

### Integration details for a partner
```sql
SELECT id, platform_name, integration_name, status, type, auth_type, created_at
FROM integrations WHERE partner = '<partner_name>';
```

### Match analysis
```sql
SELECT i.partner, i.integration_name, im.type, COUNT(*) as match_count
FROM integration_matches im
JOIN integrations i ON i.id = im.integration_id
GROUP BY i.partner, i.integration_name, im.type
ORDER BY match_count DESC LIMIT 20;
```

### Trigger breakdown
```sql
SELECT it.type, it.parameter, COUNT(*) as count
FROM integration_triggers it
JOIN integrations i ON i.id = it.integration_id
WHERE i.partner = '<partner_name>'
GROUP BY it.type, it.parameter;
```

### Webhook throttling
```sql
SELECT partner, url, rate FROM webhook_throttlings ORDER BY rate DESC;
```

## Response Format

When presenting analytics:
- Lead with a **summary sentence** answering the user's question directly
- Follow with a **data table** if applicable
- End with **insights or observations** (e.g., "80% of integrations are destinations", "Partner X has unusually high match counts")
- If the user asks a vague question, suggest 2-3 specific angles they could explore
