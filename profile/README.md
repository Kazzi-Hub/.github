# Acumace Platform Architecture

Acumace is organized around two primary repositories:

- **`acumace.core`** — the backend platform and orchestration layer
- **`acumace.ui`** — the frontend platform monorepo

This split keeps the system clean: the backend owns identity, tenant-aware business logic, authorization, persistence, async orchestration, and auditability, while the frontend owns the product experience, shared UI systems, and BFF-driven application delivery.

---

## 1. The Repositories

### `acumace.core` (The Platform Engine)

* **Repo:** `acumace.core`
* **Role:** Core backend platform for identity, tenancy, authorization, product APIs, and async orchestration.
* **Architecture:** Polyglot backend with two platform pillars:
  * **Go Core API** — the main engine
  * **Python AI Service** — the stateless cognitive layer

#### Go Core API
* **Role:** High concurrency API, orchestration, tenant enforcement, and product workflows.
* **Stack:** Go, Chi, pgx, sqlc, PostgreSQL, Redis, Cerbos, OpenTelemetry, slog
* **Responsibilities:**
  * OIDC integration, session and token handling
  * tenant resolution
  * authorization enforcement
  * CRUD and business workflows for LearnCircle, Workpods, and Console/Admin operations
  * AI job creation, dispatch, and tracking
  * SSE/WebSocket progress delivery
  * immutable audit and tenant-safe platform behavior

#### Python AI Service
* **Role:** Stateless AI execution and async cognitive workflows.
* **Stack:** FastAPI, Pydantic v2, LangGraph, Taskiq, Redis, Structlog, OpenTelemetry
* **Responsibilities:**
  * AI agent execution
  * summarization, classification, recommendation, and search workflows
  * async job consumption and result return to Core API
  * heavy text and analytics processing

#### Purpose
* Go Core API is the system of record for:
  * identity and session handling
  * tenant-aware authorization
  * product business workflows
  * audit logs
  * job metadata and results persistence
* Python AI is intentionally stateless:
  * it does not read PostgreSQL directly
  * it does not own business data
  * it only works through the Core API contract

---

### `acumace.ui` (The UI Platform)

* **Repo:** `acumace.ui`
* **Role:** Unified frontend platform for all Acumace applications.
* **Stack:** Next.js, TypeScript, Turborepo, Tailwind CSS, TanStack Query, Zustand, React Hook Form, Zod
* **Architecture Model:** Next.js monorepo with strict BFF application pattern

#### Apps Included
* `apps/learncircle-sis` — Enterprise academic and institutional administration
* `apps/console` — Cross-suite admin control plane
* `apps/workpods` — Workspace and resource operations
* `apps/learncircle-lms` — Academic delivery experience
* `apps/learncircle-portal` — Student-facing self-service experience

#### Shared Packages
* `packages/core-api` — generated API contracts and typed clients
* `packages/core-auth` — auth/session client primitives
* `packages/core-telemetry` — observability wrappers
* `packages/core-permissions` — permission-aware frontend helpers
* `packages/core-flags` — feature flag plumbing
* `packages/core-forms` — shared form handling primitives
* `packages/design-tokens` — shared design tokens
* `packages/ui-foundation` — base UI foundations
* `packages/ui-consumer` — lightweight consumer UI system
* `packages/ui-enterprise` — enterprise UI wrappers and high-density patterns
* `packages/testing` — shared test utilities

#### Purpose
* Maintain a governed UI platform instead of isolated product frontends
* Keep consumer and enterprise UI concerns separate
* Centralize transport contracts, permissions, telemetry, and design foundations
* Enforce BFF-only product delivery so browser clients do not talk directly to internal backend services

---

## 2. Platform Shape

Acumace is now a **multi-product, multi-tenant platform** built around:

- **LearnCircle** — institutional and academic product suite
- **Workpods** — multi-tenant workspace product
- **Console** — internal control plane with privileged cross-tenant visibility

### LearnCircle Suite
The LearnCircle suite now spans:
- **LearnCircle SIS**
- **LearnCircle LMS**
- **LearnCircle Portal**

The platform keeps **authentication** separate from **authorization**:

- **Authentication:** one human, one global identity
- **Authorization:** contextual access evaluated by app, tenant, role, and resource

This allows the same person to operate across products with different tenant and permission contexts without duplicating identity records.

---

## 3. Why This Split Exists

| Area | Benefit |
| --- | --- |
| **Backend discipline** | The backend stays focused on tenant-safe business logic, identity, policy enforcement, persistence, async orchestration, and auditability. |
| **Frontend discipline** | The UI monorepo stays focused on product delivery, shared design systems, generated contracts, and BFF application composition. |
| **Polyglot clarity** | Go handles high-concurrency APIs and strict platform enforcement. Python handles AI workflows without becoming the system of record. |
| **Product scale** | LearnCircle, Workpods, and Console can evolve independently while still sharing auth, permissions, contracts, and platform patterns. |
| **Type safety** | Frontend contracts are generated from backend definitions, reducing drift between API and UI. |
| **Operational safety** | Tenant isolation, Cerbos-backed authorization, idempotency, structured problem responses, and append-only audit rules remain centralized in the platform layer. |
| **UI consistency** | Shared foundations and package-level governance keep product apps aligned without forcing the same UX model on every app. |

---

## 4. Simplified Runtime and Delivery Model

### Backend Runtime
- **Go Core API** handles protected product APIs, tenancy, authz, and orchestration
- **Python AI Service** handles stateless AI workloads behind the Core API
- **Redis** supports caching, rate limiting, and async queue workflows
- **PostgreSQL** is the primary data store
- **Cerbos** evaluates authorization decisions
- **Traefik** handles edge routing into backend services
- **Kubernetes** runs the backend platform services

### Frontend Runtime
- Each Next.js application acts as a BFF
- Browser clients do not call protected backend services directly
- Product apps use generated API clients and approved data-access layers
- Consumer and enterprise applications are kept on separate UI tracks
- Heavy enterprise surfaces are handled through the enterprise UI layer and route-splitting strategy

---

## 5. Current Tech Stack

## Frontend (`acumace.ui`)

### Core Framework and Runtime
- **Framework:** Next.js
- **Language:** TypeScript
- **Monorepo:** Turborepo

### Data and State
- **Server State:** TanStack Query
- **Local App State:** Zustand
- **Forms:** React Hook Form + Zod
- **API Contracts:** generated clients in `core-api`

### UI Systems
#### Shared Foundation
- design tokens
- UI foundations
- shared form and telemetry wrappers

#### Consumer UI
- Tailwind CSS
- lightweight consumer primitives

#### Enterprise UI
- Blueprint
- AG Grid Enterprise
- Tremor
- FullCalendar

### Quality and Tooling
- **Linting/Formatting:** repo-governed frontend quality tooling
- **Testing:** Vitest, Playwright
- **CI/CD:** GitHub Actions

---

## Backend (`acumace.core`)

### Go Core API
- **Language:** Go
- **Router:** Chi
- **Database Access:** pgx + sqlc
- **Database:** PostgreSQL
- **Caching / Queue Backbone:** Redis
- **Authorization:** Cerbos
- **Auth / OIDC:** Google Identity Platform integration
- **Observability:** slog + OpenTelemetry
- **Validation:** validator
- **Testing / Security:** testify, mockery, golangci-lint, gosec

### Python AI Service
- **Framework:** FastAPI
- **Validation:** Pydantic v2
- **Agents:** LangGraph
- **Async Queue:** Taskiq + taskiq-redis
- **Observability:** Structlog + OpenTelemetry
- **Testing:** pytest + pytest-asyncio

---

## 6. Identity, Tenancy, and Security Position

### Identity
Acumace uses a global identity model:
- one human = one user record across the ecosystem

### Authorization
Access is enforced contextually by:
- app
- tenant
- role
- resource/action

### Tenancy
A tenant may represent:
- a school in LearnCircle
- a workspace in Workpods
- an internal grouping where needed

### Core security rules
- all tenant-owned data is scoped by `tenant_id`
- frontend apps do not call the Python AI service directly
- AI services do not read PostgreSQL directly
- protected flows are mediated by the Core API
- cross-tenant admin actions require explicit policy and audit coverage
- idempotency, structured errors, and immutable audit are platform rules, not optional conventions

---

## 7. Infrastructure Summary

Acumace now runs as a platform composed of:

- **Go Core API** for product logic, tenancy, authz, orchestration, and auditability
- **Python AI Service** for stateless async intelligence workflows
- **PostgreSQL** for primary persistence
- **Redis** for caching, queueing, and operational support
- **Cerbos** for policy decisions
- **Traefik** for routing
- **Kubernetes** for service orchestration
- **GitHub Actions** for CI/CD
- **Trivy** for vulnerability scanning
- **Next.js + Turborepo** for frontend application delivery

This structure gives Acumace the control, clarity, and scalability needed to grow multiple products without losing architectural discipline.
