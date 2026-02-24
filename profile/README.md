# Kazzi-Hub Platform

Kazzi-Hub is organized as a set of independent repositories. This keeps the public website, student experience, staff tooling, and core backend cleanly separated, with clear ownership, security boundaries, and deployment pipelines.

## Repositories

- **kazzihub.website** — Public-facing marketing site and student application entry point.
- **kazzihub.lms** — Student learning portal (courses, lessons, assignments, progress).
- **kazzihub.admin.portal** — Internal staff dashboard (review applicants, manage cohorts, operations).
- **kazzihub.core** — Core backend API service powering all client applications.

---

## Tech Stack (Modern & Standardized)

### Frontend (Web Apps)

#### Core Framework & Runtime

- **Framework:** [Next.js](https://nextjs.org/) (React)
- **Routing:** App Router Interface
- **Language:** TypeScript (Strict Mode)
- **Package Management:** `yarn`

#### UI & Styling

- **Styling:** [Tailwind CSS v4](https://tailwindcss.com/)
- **Components:** [shadcn/ui](https://ui.shadcn.com/)
- **Icons:** [Lucide React](https://lucide.dev/)
- **Forms:** React Hook Form + [Zod](https://zod.dev/)
- **Animations:** [Framer Motion](https://www.framer.com/motion/)

#### Data & State

- **Server State:** [TanStack Query](https://tanstack.com/query/latest) (React Query)
- **Global State:** [Zustand](https://docs.pmnd.rs/zustand/getting-started/introduction)
- **API Integration:** [openapi-ts](https://heyapi.dev/openapi-ts/) (Generated typed clients)

#### Quality & Tooling

- **Standard:** [Biome](https://biomejs.dev/) (Linting & Formatting)
- **Testing:** [Vitest](https://vitest.dev/) & [Playwright](https://playwright.dev/)

#### Deployment

- **Hosting:** [Vercel](https://vercel.com/)
- **CI/CD:** GitHub Actions

---

### Backend (Core API)

#### Core Framework & Runtime

- **Framework:** [FastAPI](https://fastapi.tiangolo.com/) (Python 3.12+)
- **Runtime/Manager:** [uv](https://github.com/astral-sh/uv) (Extremely fast Python package & project manager)
- **Server:** Uvicorn (ASGI)

#### Persistence & Data

- **Database:** PostgreSQL (Hosted on [Supabase](https://supabase.com/))
- **ORM:** SQLAlchemy 2.0 (Async)
- **Cache:** [Upstash Redis](https://upstash.com/)
- **Migrations:** Alembic
- **Driver:** `asyncpg`

#### Authentication & Security

- **Provider:** Supabase Auth (JWT)
- **Enforcement:** Role-Based Access Control (RBAC) at the API layer
- **Security:** [Ruff](https://docs.astral.sh/ruff/) (Modern Python linter & formatter)

#### Storage & Background Tasks

- **Storage:** Supabase Storage (S3-compatible)
- **Task Queue:** [Taskiq](https://taskiq-python.github.io/) (Redis-backed)

#### Deployment & Ops

- **Containerization:** Docker
- **Hosting:** Supabase (Container deployment)
- **Observability:** Sentry + Prometheus/OpenTelemetry

---

## Infrastructure Summary

All services are integrated via **Supabase** for a unified infrastructure plane (Database, Auth, Storage, Edge Runtime). Frontend sites are deployed to **Vercel** for optimal performance and edge capabilities.
