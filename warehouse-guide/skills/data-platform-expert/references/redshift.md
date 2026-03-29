# Amazon Redshift Reference

## Official Documentation
- Docs home: https://docs.aws.amazon.com/redshift/
- Developer guide: https://docs.aws.amazon.com/redshift/latest/dg/
- Go AWS SDK v2: https://aws.github.io/aws-sdk-go-v2/docs/
- Go AWS SDK GitHub: https://github.com/aws/aws-sdk-go-v2
- Redshift Data API: https://docs.aws.amazon.com/redshift/latest/dg/data-api.html
- SQL commands: https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_commands.html
- Serverless: https://docs.aws.amazon.com/redshift/latest/mgmt/working-with-serverless.html

## Go Connection Options

### Option 1: PostgreSQL Driver (Direct JDBC-style)

Redshift is PostgreSQL-compatible. Use `lib/pq` or `pgx`:

```go
import (
    "database/sql"
    _ "github.com/lib/pq"
)

dsn := fmt.Sprintf(
    "host=%s port=%d dbname=%s user=%s password=%s sslmode=require",
    os.Getenv("REDSHIFT_HOST"),
    5439,
    os.Getenv("REDSHIFT_DB"),
    os.Getenv("REDSHIFT_USER"),
    os.Getenv("REDSHIFT_PASSWORD"),
)
db, err := sql.Open("postgres", dsn)
```

### Option 2: IAM Authentication

Generate temporary database credentials via IAM:

```go
import (
    "github.com/aws/aws-sdk-go-v2/config"
    "github.com/aws/aws-sdk-go-v2/service/redshift"
)

cfg, _ := config.LoadDefaultConfig(ctx)
client := redshift.NewFromConfig(cfg)

creds, err := client.GetClusterCredentials(ctx, &redshift.GetClusterCredentialsInput{
    ClusterIdentifier: aws.String(os.Getenv("REDSHIFT_CLUSTER_ID")),
    DbName:            aws.String("mydb"),
    DbUser:            aws.String("iamuser"),
    AutoCreate:        aws.Bool(false),
    DurationSeconds:   aws.Int32(3600),
})
// Use creds.DbUser and creds.DbPassword as DB credentials
```

### Option 3: Redshift Data API (Serverless-friendly, no VPC required)

```go
import (
    "github.com/aws/aws-sdk-go-v2/service/redshiftdata"
)

dataClient := redshiftdata.NewFromConfig(cfg)

// Execute statement
result, err := dataClient.ExecuteStatement(ctx, &redshiftdata.ExecuteStatementInput{
    ClusterIdentifier: aws.String(os.Getenv("REDSHIFT_CLUSTER_ID")),
    Database:          aws.String("mydb"),
    DbUser:            aws.String("admin"),
    Sql:               aws.String("SELECT COUNT(*) FROM my_table"),
})

// Poll for completion
for {
    desc, err := dataClient.DescribeStatement(ctx, &redshiftdata.DescribeStatementInput{
        Id: result.Id,
    })
    if desc.Status == types.StatusStringFinished {
        break
    }
    time.Sleep(500 * time.Millisecond)
}

// Get results
pages, err := dataClient.GetStatementResult(ctx, &redshiftdata.GetStatementResultInput{
    Id: result.Id,
})
```

## Bulk Loading: COPY Command

The COPY command is the recommended way to load large datasets:

```sql
-- From S3
COPY my_table
FROM 's3://my-bucket/data/prefix'
IAM_ROLE 'arn:aws:iam::123456789012:role/MyRedshiftRole'
FORMAT AS CSV
IGNOREHEADER 1
TIMEFORMAT 'auto';

-- From JSON
COPY my_table
FROM 's3://my-bucket/data/'
IAM_ROLE '...'
FORMAT AS JSON 'auto';
```

## Distribution & Sort Keys

Choosing the right distribution style is critical for query performance:

```sql
-- EVEN: default, rows distributed round-robin (good for no clear join key)
CREATE TABLE events (...) DISTSTYLE EVEN;

-- KEY: rows with same key value go to same node (good for frequent joins on this column)
CREATE TABLE orders (...) DISTKEY(customer_id);

-- ALL: full copy on every node (good for small, frequently joined dimension tables)
CREATE TABLE countries (...) DISTSTYLE ALL;

-- Sort keys: keeps rows in sorted order on disk (speeds up range filters)
CREATE TABLE logs (...) SORTKEY(event_time);
-- Compound: multi-column sort
CREATE TABLE sales (...) COMPOUND SORTKEY(region, sale_date);
-- Interleaved: equal weight to all sort columns (slower VACUUM, good for multi-column filters)
CREATE TABLE events (...) INTERLEAVED SORTKEY(user_id, event_type);
```

## Common Gotchas

- **VPC requirement**: Provisioned clusters require VPC access (not public internet). Use the Data API if connecting from outside VPC, or set up a bastion/VPN.
- **Column encoding**: Redshift uses columnar compression. Run `ANALYZE COMPRESSION` after loading data to pick optimal encodings.
- **VACUUM**: Regular `VACUUM` reclaims space from deleted rows and re-sorts. Schedule during low-traffic windows. Serverless handles this automatically.
- **ANALYZE**: Run `ANALYZE` after bulk loads to update statistics for the query planner.
- **Max connections**: Provisioned clusters have hard connection limits per node type. Use a connection pooler (PgBouncer) for high-concurrency apps.
- **Spectrum vs. native tables**: Redshift Spectrum queries S3 directly but is slower than native tables. Use for infrequently accessed cold data.
- **Serverless RPU**: Redshift Serverless uses Redshift Processing Units (RPUs). Set a usage limit to control costs.

## Key Features to Know

- **RA3 Nodes**: Managed storage (data on S3, cached in local SSD). Pay for compute and storage separately.
- **Redshift Serverless**: No cluster management. Auto-scales. Billed per RPU-second. Good for unpredictable workloads.
- **Redshift Spectrum**: Query external tables in S3 without loading data. Uses same SQL interface.
- **Materialized Views**: Precomputed result sets, can be auto-refreshed. Great for dashboard queries.
- **Data Sharing**: Share live data across Redshift clusters without copying (RA3 and Serverless only).
- **Streaming Ingestion**: Ingest directly from Amazon Kinesis Data Streams or MSK (Kafka) into materialized views.
