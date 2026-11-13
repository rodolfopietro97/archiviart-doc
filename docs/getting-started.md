---
title: Getting started
description: Run the Archiviart modules locally
---

# Getting started

The Archiviart repository is a super-repository: each module is an independent
git repository included as a submodule. Run every command from inside the
relevant module folder.

| Module | What it is | Stack |
| --- | --- | --- |
| `archiviart-fs` | Local infrastructure: SeaweedFS + main PostgreSQL | Docker Compose |
| `archiviart-core` | Backend: HTTP API, OCR worker, CLI, shared services | Go workspace, Gin |
| `archiviart-monorepo` | Frontend apps (landing page, Archiviart Docs, …) | Next.js, Turborepo, yarn |
| `archiviart-smart-contract` | Contracts that anchor CEDiA evidence | Solidity, Hardhat |
| `archiviart-doc` | This documentation site | MkDocs Material |

Start the modules in this order: **archiviart-fs → archiviart-core → frontends**.

## Documentation (archiviart-doc)

Requires Python 3.11+.

```bash
cd archiviart-doc
python3 -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install .                    # python -m pip install --upgrade pip, if needed
mkdocs serve                     # http://127.0.0.1:8000
```

## Infrastructure (archiviart-fs)

The file system and the main database run in Docker containers. Start Docker,
then:

```bash
cd archiviart-fs
./start.sh                       # docker compose up -d
./stop.sh                        # stop the containers
./prune.sh                       # remove containers, volumes and images
```

| Service | Port |
| --- | --- |
| SeaweedFS filer | `8888` |
| SeaweedFS master | `9333` |
| SeaweedFS volume | `8080` |
| SeaweedFS S3 gateway | `8333` |
| PostgreSQL `archiviart_main_db` (user `archiviart`, password `password`) | `5432` |

If a native PostgreSQL already uses port 5432, set `ARCHIVIART_DB_PORT` (e.g.
`5433`) in a git-ignored `archiviart-fs/.env` and use the same port in the DSN
of `archiviart-core/.env`.

## Backend (archiviart-core)

`archiviart-core` is a Go workspace (`go.work` is committed: do not run
`go mod init` or `go work init`). Copy `.env.example` to `.env` and fill it in.

After a fresh clone, generate the Swagger package (the server imports it):

```bash
cd archiviart-core
./build-swagger.sh
```

Run the services:

```bash
go run ./modules/httpserver/cmd/main.go      # API server (:8083, /api/v1)
go run ./modules/ocrserver/cmd/main.go       # OCR worker
docker compose up --build                    # both, using .env.docker
```

Hot reload with [air](https://github.com/air-verse/air):

```bash
go install github.com/air-verse/air@latest
~/go/bin/air -v                              # verify the installation
~/go/bin/air -c .server.air.linux.toml       # .server.air.windows.toml on Windows
```

The OCR worker needs `pdftoppm`, `tesseract` and `curl` on the `PATH`.

Tests:

```bash
go test ./modules/service/...                         # unit tests
go test ./modules/integrationtests/... -v -count=1    # functional tests against the running stack
```

## Frontends (archiviart-monorepo)

Install yarn once (`npm install -g yarn`, then restart the terminal), then:

```bash
cd archiviart-monorepo
yarn install
yarn dev                         # all apps through Turborepo
```

Each authenticated app needs a `.env.local` with `AUTH_SECRET`, the
`KEYCLOAK_*` variables, `BACKEND_URL` (no trailing slash) and `NEXTAUTH_URL`.
Each app has its own port (set in its `package.json`); Archiviart Docs runs on
`http://localhost:3001`.

## Smart contracts (archiviart-smart-contract)

```bash
cd archiviart-smart-contract
npx hardhat test
```
