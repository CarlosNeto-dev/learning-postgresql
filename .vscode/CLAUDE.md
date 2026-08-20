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
      "database": "carlos_database",
      "username": "postgres",
      "name": "carlos-database"
    }
  ]
}
```

Notes on the fields, from real debugging history:

- `server: "postgres"` — must be the Docker Compose service name, **not** `localhost`. Using `localhost` here was an early source of connection failures (see root `CLAUDE.md`).
- `askForPassword: true` — set deliberately instead of caching a password via "SQLTools Driver Credentials". A stale cached password (from an earlier mistyped attempt) caused persistent `password authentication failed` errors even after the correct password was used in later UI attempts; switching to "ask on connect" and retyping fixed it for good.
- `database: "carlos_database"` — updated from the original `meubancodedados` after the database rename (see `../docker/CLAUDE.md`). If SQLTools starts a session against `meubancodedados` again, check the connection is picking up this config and not a stale editor-created duplicate connection.
