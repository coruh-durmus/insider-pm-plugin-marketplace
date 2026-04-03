# Snowflake Reference

## Official Documentation
- Docs home: https://docs.snowflake.com
- Go driver: https://docs.snowflake.com/en/developer-guide/golang/go-driver
- Go driver GitHub: https://github.com/snowflakedb/gosnowflake
- SQL reference: https://docs.snowflake.com/en/sql-reference
- REST API: https://docs.snowflake.com/en/developer-guide/snowflake-rest-api/about-snowflake-rest-api
- Authentication: https://docs.snowflake.com/en/user-guide/admin-user-management

## Go Driver Quick Start

```go
import (
    "database/sql"
    "fmt"
    sf "github.com/snowflakedb/gosnowflake"
)

// DSN format: user:password@account/database/schema?warehouse=WAREHOUSE&role=ROLE
dsn, err := sf.DSN(&sf.Config{
    Account:   os.Getenv("SNOWFLAKE_ACCOUNT"),
    User:      os.Getenv("SNOWFLAKE_USER"),
    Password:  os.Getenv("SNOWFLAKE_PASSWORD"),
    Database:  "MY_DB",
    Schema:    "PUBLIC",
    Warehouse: "COMPUTE_WH",
    Role:      "MY_ROLE",
})

db, err := sql.Open("snowflake", dsn)
```

### Key-Pair Authentication (Preferred for Services)

```go
cfg := &sf.Config{
    Account:            os.Getenv("SNOWFLAKE_ACCOUNT"),
    User:               os.Getenv("SNOWFLAKE_USER"),
    PrivateKeyPath:     "/path/to/rsa_key.p8",
    PrivateKeyPasscode: []byte(os.Getenv("SNOWFLAKE_KEY_PASSPHRASE")),
}
```

### OAuth Authentication

```go
cfg := &sf.Config{
    Account:     os.Getenv("SNOWFLAKE_ACCOUNT"),
    User:        os.Getenv("SNOWFLAKE_USER"),
    Authenticator: sf.AuthTypeOAuth,
    Token:       os.Getenv("SNOWFLAKE_OAUTH_TOKEN"),
}
```

## Connection Pool Settings

```go
db.SetMaxOpenConns(10)
db.SetMaxIdleConns(5)
db.SetConnMaxLifetime(30 * time.Minute)
```

Note: Each open connection consumes a Snowflake credit proportional to warehouse size. Keep pool sizes modest.

## Bulk Loading

Use `PUT` + `COPY INTO` for efficient bulk loads:

```go
// 1. PUT file to internal stage
_, err = db.ExecContext(ctx, "PUT file:///local/path/data.csv @MY_STAGE AUTO_COMPRESS=TRUE")

// 2. COPY INTO table from stage
_, err = db.ExecContext(ctx, `
    COPY INTO MY_TABLE
    FROM @MY_STAGE/data.csv.gz
    FILE_FORMAT = (TYPE = 'CSV' SKIP_HEADER = 1)
`)
```

## Common Gotchas

- **Account identifier format**: Use `orgname-accountname` (new) or `accountlocator.region` (legacy). The Go driver expects the account without `.snowflakecomputing.com`.
- **Virtual warehouse auto-suspend**: Warehouses auto-suspend after inactivity. First query after suspend incurs a cold-start delay (~5s).
- **VARIANT columns**: JSON data stored as VARIANT. Use `TO_JSON()` / `PARSE_JSON()` in SQL. The Go driver returns VARIANT as strings.
- **Query timeout**: Set `statement_timeout_in_seconds` session parameter or use `context.WithTimeout`.
- **Binding arrays**: Use `sf.Array()` helper for multi-row inserts.

## Key Features to Know

- **Time Travel**: Query historical data with `AT (TIMESTAMP => ...)` or `BEFORE (STATEMENT => ...)`. Default retention 1 day, Enterprise up to 90 days.
- **Streams**: CDC mechanism. `SELECT * FROM stream_name` consumes the stream (marks records as consumed).
- **Tasks**: Scheduled SQL execution. Supports DAG dependencies via `AFTER task_name`.
- **Snowpipe**: Near-real-time continuous data loading from S3/GCS/Azure Blob.
- **Dynamic Tables**: Declarative, incrementally refreshed materialized views.
