# CLAUDE.md — docker-and-databases (root)

Study environment for PostgreSQL and DBeaver, built with Claude Code, based on the *PostgreSQL & DBeaver Installation Guide* (see `academic/reference/`).

## What this repo is

A self-contained, reproducible PostgreSQL study environment: a Docker Compose stack (database + dev container) plus the course material and SQL scripts used while working through it. Everything needed to reproduce the environment lives in this repo — no manual local Postgres/DBeaver install is required (see `academic/CLAUDE.md` for the "do I need more than Docker?" analysis).

## Folder structure and purpose

```
.
├── .devcontainer/   # VS Code/Cursor Dev Container config — see .devcontainer/CLAUDE.md
├── .github/         # issue/PR templates
├── .vscode/         # editor-local settings (SQLTools connection) — see .vscode/CLAUDE.md
├── docker/          # Docker Compose stack for PostgreSQL — see docker/CLAUDE.md
├── academic/        # course/study material and SQL scripts — see academic/CLAUDE.md
├── .dockerignore    # excludes from Docker build contexts
├── .gitignore       # excludes from version control (repo-wide)
├── LICENSE          # MIT
└── README.md        # user-facing setup/usage instructions
```

Each subfolder has its own `CLAUDE.md` with folder-specific history and decisions. This root file covers cross-cutting context.

## Key architectural decision: two services, not one

The devcontainer does **not** point directly at the bare `postgres:16-alpine` image. That image is Alpine-based and lacks the glibc/tooling VS Code Server needs to attach to a container. Instead, `docker/docker-compose.yml` defines two services:

- `postgres` — the database itself (`postgres:16-alpine`), with a healthcheck.
- `workspace` — a Debian-based (`bookworm`) container built from `.devcontainer/Dockerfile`, with `postgresql-client` installed. This is what VS Code/Cursor actually attaches to. It depends on `postgres` being healthy before starting.

The devcontainer's `dockerComposeFile` points at `../docker/docker-compose.yml` and `service: workspace`.

## Editor: Cursor, not VS Code — Open VSX, not the VS Code Marketplace

The user works in **Cursor** (a VS Code fork). Cursor uses the **Open VSX** extension registry instead of the official VS Code Marketplace. Microsoft-published extensions (`ms-*`, e.g. `ms-ossdata.vscode-postgresql`) are generally unavailable there. This caused a real, multi-step debugging session (empty Explorer panel, no elephant/DB icon, extension failing to install silently) before the root cause was identified.

**Fix applied:** switched `.devcontainer/devcontainer.json`'s extension list from `ms-ossdata.vscode-postgresql` to `mtxr.sqltools` + `mtxr.sqltools-driver-pg` (open-source, available on Open VSX).

If a future extension "won't install" in Cursor with no clear error, suspect Open VSX incompatibility first and look for an open-source equivalent.

## Networking gotcha: service name vs `localhost`

Inside the Docker Compose network, the database is reachable at host `postgres` (the service name), **not** `localhost`. This tripped the user up repeatedly: opening `http://localhost:5432` in a browser (always fails — port 5432 is raw Postgres wire protocol, not HTTP, so "connection reset" is expected and correct, not a bug), and initially configuring SQLTools with the wrong Server Address. Any connection config (SQLTools, `psql`, DBeaver-over-forwarded-port) inside the devcontainer must use `postgres` as the host.

## Credentials

- Username: `postgres`
- Password: value of `POSTGRES_PASSWORD` in `docker/.env` (not versioned; `docker/.env.example` documents the shape)
- Database: `carlos_database` (see `docker/CLAUDE.md` for the rename history from `meubancodedados`)

## Git history notes

- The repo was originally organized differently (Portuguese docs, a `postgres-docker/` folder) and was reorganized into the current English-only, purpose-separated structure (`docker/`, `.devcontainer/` at root, `academic/`) at the user's explicit request.
- A stray empty `.git` repo was accidentally created inside `docker/` at one point and was removed — the only real git repo is at the project root, remote `main` → `https://github.com/CarlosNeto-dev/learning-postgre-sql`.
