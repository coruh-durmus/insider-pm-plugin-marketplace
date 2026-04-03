# warehouse-guide

Expert assistant for building Go integrations with cloud data platforms: **Snowflake**, **Google BigQuery**, **Databricks**, and **Amazon Redshift**.

## Overview

This plugin gives Claude deep knowledge of all four platforms so your team can move faster when building data integrations. It covers Go SDKs, SQL dialects, authentication patterns, bulk loading, troubleshooting, and feature research — with live documentation lookups to stay current as platforms evolve.

## Components

### Skill: `data-platform-expert`

Loaded automatically when you ask about any of the four platforms. Provides:
- Platform overviews, key concepts, and Go driver/SDK details
- SQL dialect differences across platforms
- Integration best practices (auth, connection pooling, error handling, bulk loading)
- Reference files with documentation URLs and Go code patterns for each platform

### Commands

| Command | Description | Example usage |
|---------|-------------|---------------|
| `/explain-feature` | Explain a feature or concept for a specific platform | `/explain-feature Snowflake time travel` |
| `/write-integration` | Write production-quality Go code for an integration | `/write-integration BigQuery streaming insert` |
| `/troubleshoot` | Diagnose and fix integration errors | `/troubleshoot Redshift IAM authentication error` |
| `/research-feature` | Survey what platforms offer for planning new features | `/research-feature CDC / change data capture` |

## Setup

No additional configuration or environment variables are required to install the plugin. The commands use Claude's built-in web search to fetch live documentation.

When writing or running integrations, you will need platform-specific credentials:

**Snowflake**
- `SNOWFLAKE_ACCOUNT` — account identifier (e.g. `myorg-myaccount`)
- `SNOWFLAKE_USER` — username or service account
- `SNOWFLAKE_PASSWORD` — password (or use key-pair auth: `SNOWFLAKE_PRIVATE_KEY_PATH`)

**BigQuery**
- `GOOGLE_APPLICATION_CREDENTIALS` — path to service account JSON file
- Or configure Application Default Credentials: `gcloud auth application-default login`

**Databricks**
- `DATABRICKS_HOST` — workspace URL (e.g. `https://adb-xxx.azuredatabricks.net`)
- `DATABRICKS_TOKEN` — Personal Access Token, or use OAuth M2M:
  - `DATABRICKS_CLIENT_ID` + `DATABRICKS_CLIENT_SECRET`

**Redshift**
- `REDSHIFT_HOST` — cluster endpoint
- `REDSHIFT_DB` — database name
- `REDSHIFT_USER` + `REDSHIFT_PASSWORD` — or use IAM auth with AWS credentials

## Usage

### Ask about platform features
> "How does Snowflake's task scheduler work?"
> "Explain BigQuery partitioning and clustering"

### Write integration code
> `/write-integration Snowflake bulk load from S3`
> `/write-integration Databricks query a Delta table in Go`

### Troubleshoot errors
> `/troubleshoot BigQuery error: Access Denied: Project: does not have bigquery.jobs.create permission`
> `/troubleshoot Redshift connection refused from Lambda`

### Research for feature planning
> `/research-feature real-time streaming ingestion`
> `/research-feature row-level security / access control`
> `/research-feature change data capture across all four platforms`
