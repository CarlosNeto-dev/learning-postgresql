# CLAUDE.md — docker

Docker Compose stack for the PostgreSQL study environment.

## Files

- `docker-compose.yml` — two services, `postgres` and `workspace` (see root `CLAUDE.md` for why two services).
- `.env` — actual credentials, **not versioned** (gitignored at the repo root via `docker/.env`).
- `.env.example` — template mirroring `.env`'s shape with the same current values, meant to be copied to `.env` on first setup.

## Environment variables

```
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123
POSTGRES_DB=carlos_database
```

`POSTGRES_USER` is the **login username**, not the database name — this was a real point of confusion during setup (the user initially thought `POSTGRES_USER` should hold `carlos-database`). `POSTGRES_DB` is the database name.

## Why `carlos_database` and not `carlos-database`

The user wanted the database renamed to match the project name. Hyphens in Postgres identifiers require quoting in SQL (`"carlos-database"`), which is easy to forget and error-prone. Recommended underscore instead; user confirmed `carlos_database` explicitly when asked.

## Why the rename wasn't just "change `.env` and restart"

`POSTGRES_DB` (and `POSTGRES_USER`/`POSTGRES_PASSWORD`) only take effect on **first initialization of an empty data volume**. The volume (`docker_postgres-data`) was already initialized with the old database `meubancodedados`, so editing `.env` alone did nothing to existing data — Postgres doesn't re-read these vars on every restart. To add `carlos_database` non-destructively, `CREATE DATABASE carlos_database;` was run directly against the running `postgres-db` container, preserving `meubancodedados` alongside it. Verified with `\l`. Dropping the old database was intentionally left to the user (destructive action, not taken automatically).

## Known cleanup opportunity (not yet done)

There is an orphaned volume `postgres-docker_postgres-data` left over from before the repo was reorganized (the old folder was named `postgres-docker/`). It's unused by the current Compose project (which uses `docker_postgres-data`) but was left in place rather than deleted, since removing volumes is destructive and wasn't explicitly requested.

## Common commands

```bash
docker compose up -d --build     # start (build workspace image if needed)
docker compose ps                # status
docker compose logs -f           # follow logs
docker compose down              # stop, keep data
docker compose down -v           # stop AND delete the data volume (destructive)
```

## Healthcheck

`postgres` has a `pg_isready` healthcheck; `workspace` uses `depends_on: condition: service_healthy` so it won't start against a database that isn't ready yet.
