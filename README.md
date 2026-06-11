# fh-backstage

Function Health internal developer portal, pinned to Backstage **1.51.2**.

## Plugins

The default Backstage app scaffold ships with:

| Area | Plugins |
| --- | --- |
| Catalog | Software catalog, org, catalog graph, catalog import |
| Scaffolder | Software templates with GitHub publish |
| Docs | TechDocs |
| Search | Catalog + TechDocs collators (PostgreSQL in production) |
| Platform | Kubernetes, notifications, signals, proxy |
| Auth | GitHub OAuth (production), guest (local dev only) |
| Permissions | Allow-all policy (replace before prod hardening) |

Pin is declared in `backstage.json`. Bump with `yarn backstage-cli versions:bump`.

## Local development

```bash
corepack enable
yarn install
yarn start
```

Open http://localhost:3000 (frontend) and http://localhost:7007 (backend).

## Docker image

Multi-stage build at the repo root packages the backend and bundled frontend:

```bash
docker build -t fh-backstage .
docker run --rm -it -p 7007:7007 \
  -e POSTGRES_HOST=host.docker.internal \
  -e POSTGRES_PORT=5432 \
  -e POSTGRES_USER=backstage \
  -e POSTGRES_PASSWORD=backstage \
  -e AUTH_GITHUB_CLIENT_ID=... \
  -e AUTH_GITHUB_CLIENT_SECRET=... \
  -e GITHUB_TOKEN=... \
  fh-backstage
```

Production expects PostgreSQL (`app-config.production.yaml`) and GitHub OAuth env vars.

CI builds and pushes to GHCR on release via `.github/workflows/release.yml`.

## Configuration

| File | Purpose |
| --- | --- |
| `app-config.yaml` | Local defaults (SQLite in-memory, guest + GitHub auth) |
| `app-config.production.yaml` | Container/production overrides (PostgreSQL, GitHub auth) |

Set `GITHUB_TOKEN` for catalog/scaffolder GitHub integration. Create a GitHub OAuth app for `AUTH_GITHUB_CLIENT_ID` / `AUTH_GITHUB_CLIENT_SECRET`.

## CI secrets

| Secret | Purpose |
| --- | --- |
| `AUTO_MERGE_TOKEN` | Dependabot automerge and PR labeler |

`GITHUB_TOKEN` is provided automatically by GitHub Actions (also used for GHCR publish).
