# GORM PostgreSQL Driver

## Quick Start

```go
import (
  "gorm.io/driver/postgres"
  "gorm.io/gorm"
)

// https://github.com/jackc/pgx
dsn := "host=localhost user=gorm password=gorm dbname=gorm port=9920 sslmode=disable TimeZone=Asia/Shanghai"
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{})
```

## Configuration

```go
import (
  "gorm.io/driver/postgres"
  "gorm.io/gorm"
)

db, err := gorm.Open(postgres.New(postgres.Config{
  DSN: "host=localhost user=gorm password=gorm dbname=gorm port=9920 sslmode=disable TimeZone=Asia/Shanghai", // data source name, refer https://github.com/jackc/pgx
  PreferSimpleProtocol: true, // disables implicit prepared statement usage. By default pgx automatically uses the extended protocol
}), &gorm.Config{})
```

## Existing database connection

You can initialize the dialector with an existing `*sql.DB`:

```go
import (
  "database/sql"

  "gorm.io/driver/postgres"
  "gorm.io/gorm"
)

sqlDB, err := sql.Open("pgx", dsn)
db, err := gorm.Open(postgres.New(postgres.Config{
  Conn: sqlDB,
}), &gorm.Config{})
```

## Connection hooks

GORM runs on top of `database/sql`, so hooks for newly established physical connections must be configured in the underlying driver or pool and passed to GORM via `Conn`:

```go
import (
  "context"

  "github.com/jackc/pgx/v5"
  "github.com/jackc/pgx/v5/stdlib"
  "gorm.io/driver/postgres"
  "gorm.io/gorm"
)

config, err := pgx.ParseConfig(dsn)
if err != nil {
  // handle error
}

sqlDB := stdlib.OpenDB(*config, stdlib.OptionAfterConnect(func(ctx context.Context, conn *pgx.Conn) error {
  // runs for each newly established connection
  return nil
}))

db, err := gorm.Open(postgres.New(postgres.Config{Conn: sqlDB}), &gorm.Config{})
```


Checkout [https://gorm.io](https://gorm.io) for details.
