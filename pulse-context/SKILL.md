---
name: pulse-context
description: Use when working in the Pulse campaign/member BI app repository, especially architecture, backend/frontend changes, local runs, deployment, environment variables, or dashboard behavior.
---

# Pulse Context

Use this skill for work in `/Users/admin/pulse` or requests that clearly refer to the Pulse campaign/member BI app. Treat this skill as project context and operating constraints, not as permission to deploy or mutate external systems.

## Hard Constraint

Do not run `git` commands in the Pulse repository. This includes read-only and write operations: no `git status`, `git diff`, `git add`, `git commit`, `git push`, `git pull`, `git fetch`, `git checkout`, `git branch`, `git init`, tags, PR helpers, or any command beginning with `git`.

Do not stage, commit, push, branch, initialise, reset, restore, stash, or otherwise mutate Git state. If Git information is needed, ask the user to provide it.

## Product Shape

Pulse is a campaign/member BI app with a dashboard builder and runtime dashboards.

- Monorepo structure: `backend/` is FastAPI; `frontend/` is React + Vite.
- Backend metadata and configuration live in Postgres, including dashboards, report queries, data sources, users, roles, organisations, versions, and comments.
- Report data is queried at runtime from external data sources. MySQL credentials are currently stored encrypted in Postgres.
- Admin/builder UI lives under `/admin` for data connections, queries, dashboards, chart editing, and table editing.
- Runtime dashboard view lives under `/dashboard/:slug`.
- Dashboard, chart, and table definitions are JSON-driven through `dashboards.definition`.

## Services

- `backend`: FastAPI, port `8080` externally and `8000` in container.
- `frontend`: static frontend served by Nginx in Docker, port `5180` externally and `5173` in container.
- `postgres`: metadata DB, port `55432` externally and `5432` in container.
- Compose file: `docker/docker-compose.yml`.

## Key Directories

- `backend/app/main.py`: FastAPI app and routes.
- `backend/app/repo.py`: Postgres persistence layer.
- `backend/app/migrations.py`: startup SQL migrations.
- `backend/app/reporting.py`: SQL execution against data sources and caching.
- `backend/app/auth.py` and `backend/app/security.py`: auth/session handling.
- `backend/app/ai.py`: OpenAI summary generation.
- `frontend/src/pages/Admin.tsx`: builder/admin UI.
- `frontend/src/pages/BuilderDashboard.tsx`: runtime dashboard rendering.
- `frontend/src/components/`: reusable UI controls.
- `frontend/src/utils/`: chart/table formatting and settings helpers.
- `docker/`: Docker Compose files, Dockerfiles, and Nginx frontend proxy config.

Prefer editing source files in `backend/app` and `frontend/src`. Treat `frontend/dist` and `node_modules` as generated.

## Implementation Conventions

- Keep dashboard and query behavior config-first. Persist definitions in JSON; avoid hardcoding report logic in UI.
- Builder and runtime must render the same dashboard definition. Builder-only controls should not change runtime semantics.
- Chart canvas rows are persisted through chart layout metadata. Builder and runtime must respect the same row grouping rules to avoid cross-row reflow on add/delete.
- When adding a dashboard feature, verify the runtime dashboard viewer, dashboard editor controls, and canvas layouts.
- For chart/table editor changes, preserve parity between chart-edit and table-edit actions and toolbars.
- SQL parameters in saved queries use `:ParamName` syntax; the backend rewrites them to driver placeholders.
- Data source credentials must always be encrypted before persistence with `REPO_ENCRYPTION_KEY`.
- Migrations are additive, idempotent, and executed at backend startup.
- Fresh Docker/Postgres startup does not seed report queries or a default admin user/role; schema and app settings come from startup migrations only.
- Variable placeholders use `<<VARIABLE_NAME>>` syntax. Runtime precedence is user, then role by descending role priority, then organisation, then global.
- Variable names are normalized to uppercase for lookup.
- Variable values may be stored encrypted per variable with `is_encrypted=true` using `REPO_ENCRYPTION_KEY`.

## Local Runbook

- Backend from `backend/`: `uvicorn app.main:app --reload`.
- Frontend from `frontend/`: `npm run dev`.
- Docker stack: `docker compose -f docker/docker-compose.yml up -d --build`.

Do not re-deploy after every code change. Only run Docker re-deploy when the user explicitly requests `re-deploy`.

## Railway Context

Use this only when the user asks about Railway deployment or production behavior.

- Railway project used on 2026-06-12: `positive-exploration`.
- Project ID: `41264d97-3615-4719-a446-d1c56c34ea1c`.
- Environment: `production`.
- Services: `Postgres`, `backend`, `frontend`.
- Frontend URL: `https://frontend-production-a178.up.railway.app`.
- Backend health URL: `https://backend-production-97e8.up.railway.app/health`.
- Repeatable deploy script: `scripts/deploy-railway-main.sh`.
- Railway service-local Dockerfiles: `backend/Dockerfile`, `frontend/Dockerfile`, `frontend/nginx.conf.template`.
- Older compose Dockerfiles under `docker/` are still used for local Docker Compose.

Deployment commands previously used:

```bash
railway up ./backend --path-as-root --service backend --environment production --detach
railway up ./frontend --path-as-root --service frontend --environment production --detach
```

Railway variables:

- `backend.DATABASE_URL`: set from Railway `Postgres.DATABASE_URL`.
- `backend.REPO_ENCRYPTION_KEY`: must stay stable across deploys or encrypted credentials cannot be decrypted.
- `backend.FRONTEND_ORIGIN`: frontend public Railway URL.
- `backend.PORT`: `8000`.
- `frontend.BACKEND_URL`: backend public Railway URL.
- `frontend.VITE_API_BASE`: `/api`.
- `frontend.PORT`: `5173`.

Railway private networking note: `http://backend.railway.internal:8000` timed out from Nginx during the 2026-06-12 deploy. The verified working setup is frontend Nginx proxying `/api/` to the backend public Railway URL.

Railway Postgres note: the default DB name was `railway`, not local `pulse`. Migrations must not hard-code database name `pulse`; migration `026_create_gold_readonly_user` grants connect on `current_database()`.

Useful verification commands:

```bash
curl -fsS https://backend-production-97e8.up.railway.app/health
curl -fsS https://frontend-production-a178.up.railway.app/api/health
curl -fsS https://smoggipulse.com/api/health
railway service status --service backend --json
railway service status --service frontend --json
```

## Environment Variables

Backend/runtime:

- `DATABASE_URL` is required for app metadata Postgres.
- `REPO_ENCRYPTION_KEY` is required for Fernet encryption/decryption.
- `FRONTEND_ORIGIN` is optional and defaults to `http://localhost:5180`; it is a comma-separated CORS allowlist.
- `PULSE_AUTH_BASE_URL` and `PULSE_AUTH_VALIDATE_PATH` configure external auth.
- `SIMULATE_AUTH` defaults to `false`; local/dev auth bypass.
- `REPORT_CACHE_TTL` defaults to `300`.
- `AI_CACHE_TTL` defaults to `600`.
- `OPENAI_API_KEY` enables AI summaries.
- `OPENAI_MODEL` defaults to `gpt-4o-mini`.
- `OPENAI_BASE_URL` defaults to `https://api.openai.com/v1`.

Frontend:

- `VITE_API_BASE` defaults to `http://localhost:8000`.

Compose/ops:

- `COMPOSE_PROJECT_NAME` optionally controls Docker project naming.

## Maintenance

When architecture, service topology, persisted schema, or core editor/runtime behavior changes, update the source project context document in `/Users/admin/pulse/AI_CONTEXT.md` if the user requests documentation maintenance.
