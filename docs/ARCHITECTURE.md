# Architecture

> System design, module boundaries, and data flow for Aurora LMS.
> Read alongside `DATA_MODEL.md`, `SECURITY.md`, and `TECH_STACK.md`.

## 1. Shape of the system

A modular monolith built on **Next.js (App Router)**, deployed as one application with clear
internal module boundaries, backed by **PostgreSQL** (primary), **Redis** (cache / rate limit /
queue broker), **S3-compatible storage** (media/files), and a **BullMQ** worker for async jobs.
A dedicated NestJS service is introduced **only** when a specific workload justifies it (documented
via ADR) — default is route handlers + a service layer.

```
            ┌──────────────────────────────────────────────────────────┐
   Browser  │  Next.js App Router (RSC + Client Components)             │
  ────────► │   ├─ UI (design system, shadcn/ui, Framer Motion)         │
            │   ├─ Server Actions / Route Handlers (API boundary)       │
            │   └─ Auth middleware (tenant + session resolution)        │
            └───────────────┬───────────────────────┬──────────────────┘
                            │                        │
                   ┌────────▼─────────┐     ┌────────▼─────────┐
                   │  Service layer   │     │  AI layer        │
                   │  (domain logic,  │     │  (provider abstr,│
                   │   tenant-scoped) │     │   RAG, embeddings)│
                   └───┬─────────┬────┘     └────────┬─────────┘
                       │         │                   │
              ┌────────▼──┐  ┌───▼────┐      ┌────────▼────────┐
              │ Prisma /  │  │ Redis  │      │ Vector store    │
              │ Postgres  │  │ cache  │      │ (pgvector)      │
              └───────────┘  └────────┘      └─────────────────┘
                       │
              ┌────────▼────────┐    ┌──────────────┐    ┌──────────────┐
              │ BullMQ worker   │    │ Object store │    │ Email/Notif  │
              │ (jobs, ingest,  │    │ (S3-compat)  │    │ abstraction  │
              │  AI pipelines)  │    └──────────────┘    └──────────────┘
              └─────────────────┘
```

## 2. Layered boundaries

Requests flow strictly downward; lower layers never import upward.

1. **UI layer** — React Server Components by default; Client Components only where interaction
   requires. No business logic, no direct DB access.
2. **API boundary** — Route Handlers and Server Actions. Responsibilities: authn/authz checks,
   Zod input validation, rate limiting, calling the service layer, shaping responses. Thin.
3. **Service layer** — all domain logic. Every function receives a `TenantContext` (tenant id,
   actor, roles) and is responsible for authorization decisions and audit logging. This is the
   only layer allowed to touch the data-access layer.
4. **Data-access layer** — a guarded wrapper over Prisma that **enforces tenant scoping**
   automatically (see §4). No route handler calls Prisma directly.
5. **Infrastructure** — Postgres, Redis, object storage, queues, AI providers, email — all behind
   small interfaces so they can be swapped/mocked.

## 3. Proposed source tree (created in Phase 0)

```
src/
├── app/                    # Next.js App Router (routes, layouts, pages)
│   ├── (auth)/             # sign-in, sign-up, SSO callbacks
│   ├── (app)/              # authenticated product surface
│   ├── (admin)/            # org + super-admin surfaces
│   └── api/                # route handlers (webhooks, integrations, AI streams)
├── modules/               # domain modules (vertical slices)
│   ├── auth/
│   ├── tenancy/
│   ├── rbac/
│   ├── users/
│   ├── courses/
│   ├── learning-paths/
│   ├── skills/
│   ├── assessments/
│   ├── ai/                 # tutor, generators, RAG
│   ├── analytics/
│   ├── compliance/
│   ├── gamification/
│   ├── social/
│   ├── live/
│   └── integrations/
├── server/
│   ├── db/                 # prisma client + guarded data-access helpers
│   ├── auth/               # session, providers, MFA, SSO
│   ├── ai/                 # provider abstraction, embeddings, rag
│   ├── jobs/               # BullMQ queues + workers
│   ├── storage/            # S3 abstraction
│   ├── email/              # email/notification abstraction
│   ├── audit/              # audit logging
│   ├── flags/              # feature flags
│   └── observability/      # logging, metrics, tracing hooks
├── ui/                     # design system: tokens, primitives, components
├── lib/                    # shared utils, types, zod schemas
├── env.ts                  # validated env (Zod)
└── config/                 # app config, constants
prisma/
├── schema.prisma
├── migrations/
└── seed.ts
tests/
├── unit/
├── integration/
└── e2e/
```

Each module under `src/modules/*` is a vertical slice: `schema` (Zod + types), `service`
(domain logic), `actions`/`handlers` (API boundary), `components` (UI), and `tests`.

## 4. Multi-tenancy (the spine)

- **Strategy:** shared database, shared schema, **tenant id on every tenant-owned row**
  (`organizationId`). Revisit schema-per-tenant only for a specific enterprise customer via ADR.
- **Isolation enforcement:** the data-access layer requires a `TenantContext` and injects the
  tenant filter into every read/write. Prisma client extensions / middleware reject queries on
  tenant-owned models that lack a tenant filter. There is **no** un-scoped path to tenant data.
- **Tenant resolution:** middleware resolves tenant from the authenticated session (and, for SSO,
  the IdP/domain) before any handler runs; the resolved `TenantContext` is passed down.
- **Storage isolation:** object keys are namespaced by tenant (`{tenantId}/...`); signed URLs only.
- **Cross-tenant:** only Super Admin, only through explicit, audited admin surfaces.

See `DATA_MODEL.md` and `SECURITY.md` for the full model and tests.

## 5. AuthN / AuthZ

- **AuthN:** Auth.js/Better Auth — email+password, Google, Microsoft, plus enterprise SAML & LDAP;
  MFA (TOTP). Sessions are server-validated.
- **AuthZ:** RBAC with roles (Super Admin, Org Admin, HR, Learning Manager, Instructor, Reviewer,
  Team Lead, Learner, Guest) mapped to granular permissions. Authorization decisions live in the
  service layer via a central `can(actor, action, resource)` policy module — never scattered in UI.

## 6. AI architecture

- **Provider abstraction** (`server/ai`) exposes `chat`, `stream`, `embed`, `generateObject`
  over Anthropic/OpenAI/xAI; selection by config + availability with graceful fallback.
- **RAG:** content (lessons, PDFs, transcripts) is chunked, embedded, and stored in **pgvector**,
  tenant-scoped. Retrieval is filtered by tenant + permissions before generation.
- **Pipelines** (subtitles, summaries, course generation, embeddings) run as **BullMQ jobs** with
  retries, idempotency keys, and progress events streamed to the UI.
- **Guardrails:** prompts/outputs validated with Zod (`generateObject`); cost/usage metered per
  tenant; PII never sent to providers without policy checks.

## 7. Async work & caching

- **BullMQ** on Redis for media processing, embeddings, notifications, scheduled compliance jobs.
- **Caching:** Redis for sessions, rate limits, hot reads; Next.js cache + `revalidateTag` for RSC.
- All jobs are idempotent and observable (status persisted, surfaced in admin tooling).

## 8. Observability & ops

- **Structured logging** (pino-style) with request/tenant/actor correlation ids; never log secrets/PII.
- **Tracing/metrics** hooks (OpenTelemetry-ready); error reporting via Sentry hook.
- **Health/readiness** endpoints; DB/Redis/storage checks.
- **Docker** for all services; `docker-compose` locally; GitHub Actions CI; Terraform-ready layout.

## 9. Non-functional targets

- Instant transitions, skeleton loading, zero layout shift, optimistic updates.
- p95 server action < 300ms for hot paths (excluding AI streaming).
- No N+1 queries; pagination on all lists; indexes for every tenant-scoped query.
- Responsive mobile → ultrawide; full keyboard support; WCAG 2.1 AA.
