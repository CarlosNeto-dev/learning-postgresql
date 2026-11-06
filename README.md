# Learning PostgreSQL with Docker

Study environment for PostgreSQL and DBeaver, based on the *PostgreSQL & DBeaver Installation Guide* (see `academic/`).

## Project structure

```
.
├── .devcontainer/        # VS Code Dev Container config (root-level, applies to the whole repo)
│   ├── devcontainer.json
│   └── Dockerfile        # workspace image (psql client + SQLTools)
├── .github/               # issue/PR templates
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── config.yml
│   └── PULL_REQUEST_TEMPLATE.md
├── .dockerignore          # files excluded from Docker build contexts
├── .gitignore             # files excluded from version control (repo-wide)
├── docker/               # Docker Compose setup for PostgreSQL
│   ├── docker-compose.yml
│   ├── .env              # database credentials (not versioned)
│   └── .env.example      # credentials template
└── academic/              # course/study material
    └── reference/         # reading material (installation guide PDF)
```

## Usage

### 1. Start just the database (no VS Code)

```bash
cd docker
cp .env.example .env   # if it doesn't exist yet
docker compose up -d
```

Connect with DBeaver:

- Host: `localhost`
- Port: `5432`
- Database: `db_consultpsi`
- Username: `postgres`
- Password: value of `POSTGRES_PASSWORD` in `docker/.env`

### 2. Using the Dev Container (VS Code)

1. Install the **Dev Containers** extension in VS Code.
2. Open this repository's root folder in VS Code.
3. `Cmd/Ctrl + Shift + P` → **Dev Containers: Reopen in Container**.
4. VS Code starts both services and opens inside the `workspace` container, with `psql` available and port `5432` forwarded.

Test the connection from inside the devcontainer:

```bash
psql -h postgres -U $POSTGRES_USER -d $POSTGRES_DB
```

Or use the **SQLTools** extension (installed automatically) for a GUI query editor. Add a new connection with:

- Server Address: `postgres` (the Docker Compose service name — not `localhost`)
- Port: `5432`
- Database: `db_consultpsi`
- Username: `postgres`
- Password: value of `POSTGRES_PASSWORD` in `docker/.env`

## Useful commands

Run from the `docker/` directory:

```bash
docker compose ps            # check container status
docker compose logs -f       # follow logs
docker compose down          # stop everything
docker compose down -v       # stop and delete data (volume)
```

## Troubleshooting

- **Port 5432 already in use**: change `"5432:5432"` to `"5433:5432"` in `docker/docker-compose.yml` and connect on port 5433.
- **postgres container won't start**: `docker compose logs postgres`.
- **DBeaver JDBC driver**: on first connection, accept the automatic PostgreSQL driver download.
