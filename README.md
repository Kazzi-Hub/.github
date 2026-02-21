# Project Repository Structure

```text
repo/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── deploy-api.yml
│       ├── deploy-public.yml
│       ├── deploy-admin.yml
│       └── deploy-student.yml
├── apps/
│   ├── api/                          # FastAPI service (deployable)
│   │   ├── app/
│   │   │   ├── __init__.py
│   │   │   ├── main.py               # FastAPI app entry (create_app)
│   │   │   ├── api/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── health.py
│   │   │   │   └── v1/
│   │   │   │       ├── __init__.py
│   │   │   │       ├── router.py
│   │   │   │       ├── public/
│   │   │   │       │   └── applications.py   # public submission endpoints
│   │   │   │       ├── admin/
│   │   │   │       │   ├── applications.py   # review/approve endpoints
│   │   │   │       │   └── users.py          # RBAC/admin ops
│   │   │   │       └── student/
│   │   │   │           └── courses.py        # learning endpoints
│   │   │   ├── core/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── config.py           # settings/env
│   │   │   │   ├── logging.py
│   │   │   │   ├── security.py         # JWT/session, hashing, etc.
│   │   │   │   └── permissions.py      # RBAC policies
│   │   │   ├── db/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── session.py          # engine/session
│   │   │   │   ├── base.py
│   │   │   │   ├── models/             # SQLAlchemy models
│   │   │   │   ├── migrations/         # Alembic versions/ (or /alembic)
│   │   │   │   └── repositories/       # DB access layer
│   │   │   ├── domain/
│   │   │   │   ├── __init__.py
│   │   │   │   ├── applicants/         # business rules (status machine)
│   │   │   │   ├── admissions/
│   │   │   │   └── learning/
│   │   │   ├── services/
│   │   │   │   ├── email.py
│   │   │   │   ├── storage.py
│   │   │   │   ├── notifications.py
│   │   │   │   └── integrations/
│   │   │   │       ├── mailgun.py / ses.py
│   │   │   │       └── s3.py / gcs.py
│   │   │   ├── jobs/
│   │   │   │   ├── queue.py            # task queue wiring (RQ/ARQ/Celery/etc)
│   │   │   │   └── workers.py
│   │   │   └── utils/
│   │   │       ├── ids.py
│   │   │       └── time.py
│   │   ├── tests/
│   │   │   ├── unit/
│   │   │   └── integration/
│   │   ├── pyproject.toml            # (poetry/uv/pdm) + ruff/mypy config
│   │   ├── alembic.ini               # if using Alembic
│   │   ├── Dockerfile
│   │   └── README.md
│   ├── public-web/                   # marketing + apply form (deployable)
│   │   ├── src/
│   │   │   ├── app/                  # Next.js App Router (if Next)
│   │   │   ├── components/
│   │   │   ├── features/
│   │   │   │   ├── apply/
│   │   │   │   └── programs/
│   │   │   └── lib/
│   │   ├── public/
│   │   ├── package.json
│   │   ├── next.config.js
│   │   └── README.md
│   ├── admin-portal/                 # internal admin dashboard (deployable)
│   │   ├── src/
│   │   │   ├── app/
│   │   │   ├── components/
│   │   │   ├── features/
│   │   │   │   ├── applicants/
│   │   │   │   ├── reviews/
│   │   │   │   └── settings/
│   │   │   └── lib/
│   │   ├── package.json
│   │   ├── next.config.js
│   │   └── README.md
│   └── student-portal/               # learning portal (deployable)
│       ├── src/
│       │   ├── app/
│       │   ├── components/
│       │   ├── features/
│       │   │   ├── courses/
│       │   │   ├── lessons/
│       │   │   ├── assignments/
│       │   │   └── progress/
│       │   └── lib/
│       ├── package.json
│       ├── next.config.js
│       └── README.md
├── packages/
│   ├── ui/                           # shared design system components
│   │   ├── src/
│   │   │   ├── components/
│   │   │   └── styles/
│   │   └── package.json
│   ├── api-client/                   # generated client from OpenAPI (recommended)
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── package.json
│   ├── types/                        # shared types/contracts
│   │   ├── openapi/
│   │   │   └── openapi.json          # exported from FastAPI
│   │   ├── src/
│   │   │   └── index.ts
│   │   └── package.json
│   ├── validators/                   # shared validation schemas (zod/yup)
│   │   ├── src/
│   │   │   ├── application.ts
│   │   │   └── auth.ts
│   │   └── package.json
│   └── config/                       # shared tooling config (eslint/ts/biome/prettier)
│       ├── eslint/
│       ├── typescript/
│       └── package.json
├── infra/
│   ├── docker/
│   │   └── docker-compose.yml        # local dev (db, redis, etc.)
│   └── scripts/
│       ├── dev.sh                    # run all apps locally
│       ├── export-openapi.sh         # pulls schema from api -> packages/types
│       └── generate-client.sh        # generates TS client into packages/api-client
├── docs/
│   ├── architecture/
│   │   ├── adr/                      # architecture decision records
│   │   └── diagrams/
│   └── runbooks/
├── .env.example
├── README.md
├── package.json                      # workspace root (pnpm/yarn)
├── pnpm-workspace.yaml               # if pnpm
└── turbo.json                        # if turborepo
```
