# CLAUDE.md — academic

Course/study material for the PostgreSQL & DBeaver installation guide, separated from the infrastructure (`docker/`, `.devcontainer/`) at the user's explicit request when the repo was reorganized ("o que é acadêmico deixe em academic").

## Structure

```
academic/
└── reference/   # reading material (the installation guide PDF) — see reference/CLAUDE.md
```

There used to be an `academic/sql/` folder holding SQLTools' auto-generated `.session.sql` scratch files. It was removed — those files are just throwaway query scratchpads, not meaningful study material worth versioning, and keeping a folder around just to catch them added structure without value. `*.session.sql` is now gitignored at the repo root instead (see root `.gitignore`), so if SQLTools drops one there again it won't clutter the repo or need to be tracked/organized.

## "Is Docker alone enough to do everything the guide describes?"

The user asked whether the Docker `postgres` service by itself was sufficient to complete everything in `reference/postgresql-dbeaver-installation-guide.pdf`, or whether a real DBeaver install was also needed. Answer given: the Docker service fully covers Part 1 of the guide (getting a running PostgreSQL instance — the guide's own "Docker" install path is literally what this repo automates) and, combined with `psql`/SQLTools in the devcontainer, covers the querying/connecting concepts in Part 3 as well. What Docker does **not** provide is DBeaver itself — Part 2 of the guide (installing DBeaver as a desktop GUI client) still requires installing the actual DBeaver application separately if the user specifically wants to practice with that client rather than SQLTools. SQLTools inside the devcontainer is a practical substitute for the querying workflow, but is not DBeaver.
