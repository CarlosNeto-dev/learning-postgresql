# CLAUDE.md — .devcontainer

VS Code/Cursor Dev Container config for this repo. Root-level (not under `docker/`) so it applies to the whole repository, per explicit user request when the folder structure was first set up.

## Files

- `devcontainer.json` — points at `../docker/docker-compose.yml`, uses the `workspace` service, forwards port 5432, and installs `mtxr.sqltools` + `mtxr.sqltools-driver-pg`.
- `Dockerfile` — builds the `workspace` service image: `mcr.microsoft.com/devcontainers/base:bookworm` + `postgresql-client`.

## Why a separate `workspace` service instead of building on `postgres:16-alpine` directly

Alpine lacks the glibc/tooling VS Code Server needs to attach to a running container. Pointing `devcontainer.json` straight at the `postgres` service would not work. `Dockerfile` here builds a Debian (`bookworm`) image instead, and `docker-compose.yml`'s `workspace` service uses it, with `depends_on: postgres: condition: service_healthy` so it only starts once the database is actually ready.

## Extensions: why SQLTools instead of `ms-ossdata.vscode-postgresql`

The user's editor is **Cursor**, which pulls extensions from **Open VSX**, not the VS Code Marketplace. Microsoft's `ms-*` extensions are typically not published there. The original plan (`ms-ossdata.vscode-postgresql`) silently failed to install, which manifested as a confusing empty Explorer/no elephant icon before the root cause was found. Switched to `mtxr.sqltools` + `mtxr.sqltools-driver-pg`, which are on Open VSX.

**If extensions in this list stop working:** check whether they're Microsoft-published; if so, look for an Open VSX-compatible alternative rather than assuming the config is broken.

## Rebuilding after changing this config

Changes to `devcontainer.json` (e.g. the extensions list) do **not** take effect on a simple reload — the container must actually be rebuilt. Use Command Palette → **"Dev Containers: Rebuild Container"**. A stale container ID across screenshots was exactly how a "missing extension" bug was diagnosed here.

## Connecting from inside the container

Host is `postgres` (the Compose service name), never `localhost`:

```bash
psql -h postgres -U $POSTGRES_USER -d $POSTGRES_DB
```

Or via the SQLTools GUI — see root `README.md` for the exact connection fields, and `../.vscode/CLAUDE.md` for the saved connection config.

## SQLTools password caching gotcha

SQLTools can cache a connection's password in the OS keychain ("SQLTools Driver Credentials"). If a password was mistyped once, later correct attempts can still fail with `password authentication failed` because the stale cached value is reused. Fix: delete and recreate the connection, or set it to **"Ask on connect"** instead of caching, and retype the password carefully. Verified independently at the time via `docker exec postgres-workspace ... psql ...` to confirm the DB/network side was fine before concluding it was a client-side caching issue.
