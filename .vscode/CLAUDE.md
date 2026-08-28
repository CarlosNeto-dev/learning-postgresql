# CLAUDE.md — .vscode

Editor-local settings, not project infrastructure — kept separate from `.devcontainer/` (which is portable/shared config) on purpose.

## `settings.json`

Holds the SQLTools connection used throughout this project's setup and debugging:

```json
{
  "sqltools.connections": [
    {
      "server": "postgres",
      "port": 5432,
      "askForPassword": true,
      "driver": "PostgreSQL",
      "database": "db_consultpsi",
      "username": "postgres",
      "name": "postgres-databases-studies"
    }
  ]
}
```

Notes on the fields, from real debugging history:

- `server: "postgres"` — must be the Docker Compose service name, **not** `localhost`. Using `localhost` here was an early source of connection failures (see root `CLAUDE.md`).
- `askForPassword: true` — set deliberately instead of caching a password via "SQLTools Driver Credentials". A stale cached password (from an earlier mistyped attempt) caused persistent `password authentication failed` errors even after the correct password was used in later UI attempts; switching to "ask on connect" and retyping fixed it for good.
- `database: "db_consultpsi"` — the database name went through two renames (`meubancodedados` → `carlos_database` → `db_consultpsi`, see `../docker/CLAUDE.md`), the last one to match the name used by the course instructor for the same exercise. If SQLTools starts a session against an old name again, check this config is being picked up and not a stale editor-created duplicate connection.
- `name: "postgres-databases-studies"` — the connection's own label, deliberately **different from any database name**. A connection is an access channel to the Postgres server that happens to default into one database; naming it after that database (as was done initially) caused confusion between "this is a connection" vs "this is a database". The name is free text and doesn't affect behavior.
