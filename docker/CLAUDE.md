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
POSTGRES_DB=db_consultpsi
```

`POSTGRES_USER` is the **login username**, not the database name — this was a real point of confusion during setup (the user initially thought `POSTGRES_USER` should hold `carlos-database`). `POSTGRES_DB` is the database name.

## Database naming history

The database went through two names before settling:

1. `meubancodedados` — the original name from initial setup.
2. `carlos_database` — renamed to match the project/repo name. Hyphens in Postgres identifiers require quoting in SQL (`"carlos-database"`), which is easy to forget and error-prone, so underscore was used instead of the initially-proposed `carlos-database`.
3. `db_consultpsi` — final rename, to match the name the user's course instructor uses for the same exercise/dataset, so the study material lines up 1:1 with what's taught in class.

## Why a rename isn't just "change `.env` and restart"

`POSTGRES_DB` (and `POSTGRES_USER`/`POSTGRES_PASSWORD`) only take effect on **first initialization of an empty data volume**. Editing `.env` alone does nothing to an already-initialized volume — Postgres doesn't re-read these vars on every restart. Each rename above was done live against the running `postgres-db` container instead:

```sql
\c postgres
ALTER DATABASE old_name RENAME TO new_name;   -- or:
CREATE DATABASE new_name;                     -- to add non-destructively, then
DROP DATABASE old_name;                       -- to remove the old one once no longer needed
```

`meubancodedados` → `carlos_database` was done as a non-destructive `CREATE DATABASE` (both existed side by side for a while) followed later by a `DROP DATABASE meubancodedados` once confirmed unused. `carlos_database` → `db_consultpsi` followed the same pattern: `db_consultpsi` was created fresh, then `carlos_database` was dropped once the user confirmed it should go (had to terminate an active SQLTools session against it first, via `pg_terminate_backend`, since Postgres won't drop a database with open connections).

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
