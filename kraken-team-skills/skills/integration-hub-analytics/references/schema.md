# Integration Hub Database Schema

Database: `integration-hub-db` (MySQL 8, read-only access)

## Tables Overview

| Table | Rows (approx) | Purpose |
|-------|---------------|---------|
| `integrations` | 9,131 | Core table — every partner integration |
| `integration_matches` | 111,962 | Parameter mappings between Insider and CDP |
| `integration_attributes` | — | Key-value metadata per integration |
| `integration_triggers` | — | Trigger definitions per integration |
| `integration_trigger_event_conditions` | — | Event-based trigger conditions |
| `integration_trigger_attribute_conditions` | — | Attribute-based trigger conditions |
| `mapping_types` | — | Mapping type definitions |
| `mappings` | 0 | Destination/source mappings (currently empty) |
| `snowflake_partner_roles` | — | IAM roles for Snowflake partners |
| `webhook_throttlings` | — | Rate limits per partner webhook |

## Table Details

### `integrations` (core table)

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Integration ID |
| `auth_type` | int | Authentication type (numeric code) |
| `platform_name` | varchar(255) | Platform name |
| `partner` | varchar(255) | Partner identifier (indexed) |
| `integration_name` | varchar(255) | Human-readable integration name |
| `status` | varchar(255) | `active`, `available`, `draft`, `paused`, `not_available`, `action_required` |
| `type` | varchar(255) | `destination`, `source`, `source_and_destination` |
| `is_pinned` | tinyint(1) | Whether integration is pinned |
| `notes` | text | Free-text notes |
| `segmentation` | longtext | Segmentation config (JSON) |
| `created_by` | varchar(255) | Creator |
| `updated_by` | varchar(255) | Last updater |
| `created_at` | timestamp | Creation timestamp |
| `updated_at` | timestamp | Last update timestamp |
| `ucd_key` | varchar(255) | UCD key |
| `ucd_key_id` | int | UCD key ID |

### `integration_matches`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Match ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `type` | varchar(255) | Match type |
| `insider_parameter` | varchar(255) | Insider-side parameter name |
| `cdp_parameter` | varchar(512) | CDP-side parameter name |
| `is_deactivated` | tinyint(1) | Whether match is deactivated |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `integration_attributes`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Attribute ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `key` | varchar(255) | Attribute key |
| `value` | text | Attribute value |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `integration_triggers`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Trigger ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `type` | varchar(255) | Trigger type |
| `parameter` | varchar(255) | Trigger parameter |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `integration_trigger_event_conditions`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Condition ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `event` | varchar(255) | Event name |
| `event_parameter` | varchar(255) | Event parameter |
| `operator` | varchar(16) | Comparison operator |
| `value` | varchar(255) | Condition value |
| `range_unit` | varchar(16) | Range unit |
| `group_hash` | varchar(255) | Group hash for condition grouping |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `integration_trigger_attribute_conditions`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Condition ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `condition` | varchar(3) | Condition type (AND/OR) |
| `attribute` | varchar(255) | Attribute name |
| `operator` | varchar(16) | Comparison operator |
| `values` | text | Condition values |
| `values_to` | text | Range upper bound |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `mapping_types`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Type ID |
| `integration_id` | bigint unsigned FK | Links to `integrations.id` |
| `type` | varchar(255) | Mapping type |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `mappings`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Mapping ID |
| `type_id` | int FK | Links to `mapping_types.id` |
| `destination` | varchar(255) | Destination field |
| `source` | varchar(255) | Source field |
| `created_at` / `updated_at` | timestamp | Timestamps |

### `snowflake_partner_roles`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Role ID |
| `partner_name` | varchar(255) | Partner name (indexed) |
| `role_arn` | varchar(2048) | AWS IAM role ARN |
| `external_id` | varchar(1224) | External ID for STS |
| `allowed_path` | varchar(255) | Allowed S3 path |
| `created_at` | timestamp | Creation timestamp |

### `webhook_throttlings`

| Column | Type | Description |
|--------|------|-------------|
| `id` | bigint unsigned PK | Throttle ID |
| `partner` | varchar(255) | Partner name |
| `url` | varchar(1000) | Webhook URL |
| `rate` | int | Rate limit |
| `created_at` / `updated_at` | timestamp | Timestamps |

## Key Relationships

```
integrations (1) ──── (N) integration_matches
integrations (1) ──── (N) integration_attributes
integrations (1) ──── (N) integration_triggers
integrations (1) ──── (N) integration_trigger_event_conditions
integrations (1) ──── (N) integration_trigger_attribute_conditions
integrations (1) ──── (N) mapping_types (1) ──── (N) mappings
```

All child tables join to `integrations` via `integration_id` foreign key.
