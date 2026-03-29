# Databricks Reference

## Official Documentation
- Docs home: https://docs.databricks.com
- Go SDK: https://docs.databricks.com/en/dev-tools/sdk-go.html
- Go SDK GitHub: https://github.com/databricks/databricks-sdk-go
- SQL connector for Go: https://github.com/databricks/databricks-sql-go
- REST API: https://docs.databricks.com/api/
- Delta Lake: https://docs.delta.io/latest/index.html
- Unity Catalog: https://docs.databricks.com/en/data-governance/unity-catalog/index.html

## Go SDK Quick Start

The Databricks Go SDK is used for workspace management (clusters, jobs, secrets, etc.):

```go
import "github.com/databricks/databricks-sdk-go"

w, err := databricks.NewWorkspaceClient(&databricks.Config{
    Host:  os.Getenv("DATABRICKS_HOST"),  // e.g. https://adb-xxx.azuredatabricks.net
    Token: os.Getenv("DATABRICKS_TOKEN"), // PAT
})
```

### OAuth M2M Authentication (Preferred for Services)

```go
w, err := databricks.NewWorkspaceClient(&databricks.Config{
    Host:         os.Getenv("DATABRICKS_HOST"),
    ClientID:     os.Getenv("DATABRICKS_CLIENT_ID"),
    ClientSecret: os.Getenv("DATABRICKS_CLIENT_SECRET"),
})
```

### Auto-Auth (Default Chain)

The SDK tries: env vars → config file (`~/.databrickscfg`) → Azure Managed Identity → Instance profile. No explicit config needed if environment is set up.

## SQL Queries via JDBC-compatible Connector

For running SQL against Databricks SQL Warehouses or All-Purpose clusters:

```go
import (
    "database/sql"
    _ "github.com/databricks/databricks-sql-go"
)

connector, err := dbsql.NewConnector(
    dbsql.WithServerHostname(os.Getenv("DATABRICKS_HOST")),
    dbsql.WithHTTPPath(os.Getenv("DATABRICKS_HTTP_PATH")), // SQL Warehouse HTTP path
    dbsql.WithAccessToken(os.Getenv("DATABRICKS_TOKEN")),
)

db := sql.OpenDB(connector)
defer db.Close()

rows, err := db.QueryContext(ctx, "SELECT * FROM my_catalog.my_schema.my_table LIMIT 100")
```

## Running Jobs via SDK

```go
// Run a job and wait for completion
run, err := w.Jobs.RunNowAndWait(ctx, jobs.RunNow{
    JobID: 12345,
    NotebookParams: map[string]string{
        "input_date": "2024-01-01",
    },
})
```

## Delta Lake Patterns

Databricks uses Delta Lake as its default table format. Key operations from SQL:

```sql
-- Merge (upsert)
MERGE INTO target t
USING source s ON t.id = s.id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;

-- Time travel
SELECT * FROM my_table VERSION AS OF 5;
SELECT * FROM my_table TIMESTAMP AS OF '2024-01-01';

-- Optimize + Z-ordering (co-locate related data)
OPTIMIZE my_table ZORDER BY (customer_id, event_date);

-- Vacuum (remove old files)
VACUUM my_table RETAIN 168 HOURS;
```

## Common Gotchas

- **Cluster startup time**: All-Purpose clusters take 3-7 minutes to start. Use SQL Warehouses (pre-warmed) or scheduled jobs for latency-sensitive integrations.
- **HTTP path vs. cluster ID**: SQL connector needs the SQL Warehouse HTTP path (found in Databricks UI under SQL Warehouses → Connection Details), not the cluster ID.
- **Unity Catalog naming**: Three-level namespace: `catalog.schema.table`. Legacy Hive Metastore uses `schema.table` (two levels).
- **Token scopes**: PATs have full workspace access. Use OAuth M2M with fine-grained permissions for production.
- **Delta transaction log**: Every write creates a new transaction log entry. Avoid many small writes (use micro-batches or Auto Loader instead).
- **Python/Scala interop**: The SQL connector and Go SDK cannot execute notebook cells directly. Use Jobs API to trigger notebooks.

## Key Features to Know

- **Auto Loader** (`cloudFiles`): Incremental file ingestion from cloud storage (S3, GCS, ADLS). Tracks processed files automatically.
- **Structured Streaming**: Continuous/micro-batch processing with Delta as a sink. Exactly-once semantics.
- **Unity Catalog**: Centralized data governance across workspaces. Supports fine-grained column-level access control.
- **Serverless Compute**: SQL Warehouses and Jobs can run serverless (no cluster management). Faster startup, pay-per-use.
- **MLflow**: Built-in experiment tracking and model registry. Accessible via REST API or Go SDK.
- **Vector Search**: Native vector store for embedding-based similarity search (recent feature — always fetch latest docs).
