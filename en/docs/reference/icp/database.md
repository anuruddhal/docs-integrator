---
title: Database Configuration
description: Database and credentials database configuration reference for the ICP Server.
---

# Database Configuration

## Main Database

Configure the main database in the `[icp_server.storage]` section of `<ICP_HOME>/conf/deployment.toml`.

| Key        | Description                                               |
| ---------- | --------------------------------------------------------- |
| `dbType`   | Database engine — `mysql`, `postgresql`, `mssql`, or `h2` |
| `host`     | Database server hostname (not used for H2)                |
| `port`     | Database server port (not used for H2)                    |
| `name`     | Database/schema name                                      |
| `username` | Database user                                             |
| `password` | Database password                                         |

### MySQL

```toml
[icp_server.storage]
dbType = "mysql"
host = "localhost"
port = 3306
name = "icp_db"
username = "root"
password = "root"
```

### PostgreSQL

```toml
[icp_server.storage]
dbType = "postgresql"
host = "localhost"
port = 5432
name = "icp_db"
username = "postgres"
password = "postgres"
```

### Microsoft SQL Server

```toml
[icp_server.storage]
dbType = "mssql"
host = "localhost"
port = 1433
name = "icp_db"
username = "SA"
password = "YourStrong@Passw0rd"
```

### H2 (In-Memory)

```toml
[icp_server.storage]
dbType = "h2"
```

H2 is suitable for development and testing only. Data is not persisted across restarts.

---

## Credentials Database

The default authentication backend stores user credentials in a separate database or schema, configured via the flat keys below.

| Key                     | Type     | Default           | Description                             |
| ----------------------- | -------- | ----------------- | --------------------------------------- |
| `credentialsDbType`     | `string` | `"h2"`            | `h2`, `postgresql`, `mysql`, or `mssql` |
| `credentialsDbHost`     | `string` | `"localhost"`     | Not used for H2                         |
| `credentialsDbPort`     | `int`    | `5432`            | Not used for H2                         |
| `credentialsDbName`     | `string` | `"credentialsdb"` | Database/schema name                    |
| `credentialsDbUser`     | `string` | `"icp_user"`      | Database user                           |
| `credentialsDbPassword` | `string` | —                 | Database password                       |

For PostgreSQL, credentials are stored in a `credentials` schema within the same database. For H2, they are stored in `./database/credentials`.
