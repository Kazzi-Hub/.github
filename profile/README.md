# Kazzi-Hub Platform Infrastructure

Kazzi-Hub is organized into two primary repositories. This structure provides a clean separation of concerns between the **Business Logic (Python)** and the **User Experience (TypeScript)**, while enabling seamless sharing of UI components and authentication logic across all subdomains.

## 1. The Repositories

### `kazzihub-core` (The Engine)
* **Repo:** [`kazzihub.core`](https://github.com/Kazzi-Hub/kazzihub.core)
* **Role:** Central Backend API & Identity Management.
* **Stack:** FastAPI, Python 3.12+, PostgreSQL (Supabase), Upstash Redis, Taskiq.
* **Purpose:** * Single source of truth for the database and RBAC logic.
* Handles the SSO handshake and JWT issuance.
* Manages long-running background tasks and file storage.



### `kazzihub-monorepo` (The Interface)
* **Repo:** [`kazzihub.apps`](https://github.com/Kazzi-Hub/kazzihub.apps)
* **Role:** Unified Frontend Workspace for all subdomains.
* **Stack:** Next.js, TypeScript, Tailwind CSS, Turborepo (orchestration).
* **Apps Included:**
* `apps/marketing` (`kazzihub.com`) — Public site & SEO.
* `apps/lms` (`lms.kazzihub.com`) — Student learning portal.
* `apps/staff` (`admin.kazzihub.com`) — Operations & cohort management.
* `apps/auth` (`auth.kazzihub.com`) — The central SSO login experience.


* **Purpose:** * Maintains a consistent design system across all products.
* Shared `@kazzihub/auth-client` package for uniform login/logout behavior.
* Atomic deployments: updates to the shared UI library propagate to all apps instantly.

---

## 2. Why This Two-Repo Split?

| Feature | Benefit |
| --- | --- |
| **Type Safety** | We will use `kazzihub-core` OpenAPI spec to generate a TypeScript client for the `kazzihub-monorepo`, ensuring backend and frontend are always in sync. |
| **Shared Auth** | The complex "Host-Only Cookie" and "PKCE Exchange" logic is written **once** in a shared library inside the monorepo and used by all apps. |
| **Developer UX** | Backend devs will stay in a pure Python environment (`uv`, `ruff`); Frontend devs stay in a pure TS environment without cross-language friction. |
| **Branding** | Updates to the Kazzi-Hub logo or color palette are made in one shared package and reflected across LMS, Staff, and Marketing apps. |

---

## 3. Simplified Deployment Strategy

* **`kazzihub-core`:** Deployed as a Docker container on Supabase or a specialized ASGI host. This repo manages its own database migrations (`Alembic`).
* **`kazzihub-monorepo`:** Deployed on Vercel. Vercel automatically detects the Turborepo structure, deploying `lms.kazzihub.com` and `admin.kazzihub.com` as independent projects while sharing the same underlying code library.

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
