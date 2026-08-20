# CLAUDE.md — academic/reference

Reading material for this study project.

## Contents

- `postgresql-dbeaver-installation-guide.pdf` — the original *Guia de Instalação PostgreSQL & DBeaver* (V3) the user provided at the start of this project, renamed to an English filename during the repo reorg and moved here (originally at repo root, briefly passed through an `academic/font/` location that appeared unexplained and was flagged but not reverted, before settling here).

## What's in the guide (for context)

The PDF covers, in order:
1. Installing PostgreSQL — both the traditional local install and via Docker.
2. Installing DBeaver (desktop GUI client).
3. Connecting DBeaver to Postgres and basic usage.
4. Troubleshooting.

This repo's `docker/` + `.devcontainer/` setup automates Part 1's Docker path end-to-end. See `../CLAUDE.md` for the analysis of what the guide requires beyond what Docker alone provides (short version: DBeaver itself, if the user wants that specific client rather than SQLTools).
