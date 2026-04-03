# Google BigQuery Reference

## Official Documentation
- Docs home: https://cloud.google.com/bigquery/docs
- Go client library: https://cloud.google.com/bigquery/docs/reference/libraries#go
- Go client GitHub: https://github.com/googleapis/google-cloud-go/tree/main/bigquery
- SQL reference: https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax
- REST API: https://cloud.google.com/bigquery/docs/reference/rest
- Pricing: https://cloud.google.com/bigquery/pricing

## Go Client Quick Start

```go
import "cloud.google.com/go/bigquery"

ctx := context.Background()
client, err := bigquery.NewClient(ctx, os.Getenv("GCP_PROJECT_ID"))
if err != nil {
    return fmt.Errorf("bigquery.NewClient: %w", err)
}
defer client.Close()
```

### Authentication

**Application Default Credentials (ADC)** — recommended for services running on GCP (GCE, GKE, Cloud Run):

```bash
# Local development
gcloud auth application-default login

# In code — no explicit auth needed, ADC is automatic
client, err := bigquery.NewClient(ctx, projectID)
```

**Service Account Key** (for non-GCP environments):

```go
import "google.golang.org/api/option"

client, err := bigquery.NewClient(ctx, projectID,
    option.WithCredentialsFile("/path/to/service-account.json"),
)
// Or via env var: GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json
```

**Workload Identity** (GKE — preferred over SA keys):
- Annotate the Kubernetes ServiceAccount and bind it to a GCP service account. No key files needed.

## Running Queries

```go
q := client.Query(`
    SELECT name, COUNT(*) as cnt
    FROM ` + "`project.dataset.table`" + `
    WHERE created_at > @start_date
    GROUP BY name
    ORDER BY cnt DESC
    LIMIT 100
`)
q.Parameters = []bigquery.QueryParameter{
    {Name: "start_date", Value: time.Now().AddDate(0, -1, 0)},
}

it, err := q.Read(ctx)
for {
    var row []bigquery.Value
    err := it.Next(&row)
    if err == iterator.Done {
        break
    }
}
```

## Streaming Inserts (Real-Time)

```go
ins := client.Dataset("my_dataset").Table("my_table").Inserter()
items := []*MyStruct{...}
if err := ins.Put(ctx, items); err != nil {
    // Check for PartialInsertionError
}
```

Note: Streaming inserts have per-row size limits (10 MB) and are not immediately queryable in some edge cases. Not suitable for large batch loads.

## Load Jobs (Batch)

```go
gcsRef := bigquery.NewGCSReference("gs://my-bucket/data/*.csv")
gcsRef.SourceFormat = bigquery.CSV
gcsRef.SkipLeadingRows = 1
gcsRef.AutoDetect = true

loader := client.Dataset("my_dataset").Table("my_table").LoaderFrom(gcsRef)
loader.WriteDisposition = bigquery.WriteTruncate

job, err := loader.Run(ctx)
status, err := job.Wait(ctx)
```

## Common Gotchas

- **Billing project vs. data project**: Queries are billed to the client's project, not the data's project. Set `client.Project()` or pass the billing project explicitly.
- **Slot contention**: On-demand pricing means large queries compete for slots. Use reservations for predictable workloads.
- **Partitioned tables**: Always filter on the partition column (`_PARTITIONTIME` or a DATE/TIMESTAMP column) to avoid full table scans.
- **Table references**: Always use backticks around fully qualified names: `` `project.dataset.table` ``.
- **Streaming buffer**: Newly streamed rows may not appear in `TABLE_DATE_RANGE` or `$yyyymmdd` partition selectors immediately.
- **DML costs**: `UPDATE`, `DELETE`, `MERGE` operations scan the full table. Use with care on large tables.

## Key Features to Know

- **Partitioning**: Partition by ingestion time (`_PARTITIONTIME`), a DATE/TIMESTAMP column, or an INTEGER range. Reduces query cost dramatically.
- **Clustering**: Co-locate related rows on disk. Combine with partitioning for best results. Up to 4 clustering columns.
- **Materialized Views**: Pre-computed query results, incrementally updated. Great for aggregations queried frequently.
- **BigQuery Storage API**: Fast read via Arrow or Avro format for large result sets. Use `cloud.google.com/go/bigquery/storage/apiv1`.
- **BI Engine**: In-memory analysis service for sub-second queries on dashboards.
- **INFORMATION_SCHEMA**: Query metadata about datasets, tables, jobs: `SELECT * FROM region-us.INFORMATION_SCHEMA.JOBS_BY_PROJECT`.
