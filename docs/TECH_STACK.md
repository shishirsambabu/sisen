# Tech Stack & Rationale

Pin to the **latest stable** version at init time; record exact versions in `package.json` and
lockfile. Don't add a new major dependency without an ADR.

## Frontend

| Choice | Why |
|---|---|
| **Next.js (App Router)** | RSC, streaming, server actions, file routing, first-class Vercel/Docker deploy. |
| **React + TypeScript (strict)** | Industry standard; strict typing is non-negotiable here. |
| **Tailwind CSS** | Token-driven, consistent 8px-grid system, fast iteration, no CSS sprawl. |
| **shadcn/ui** | Unstyled-but-accessible primitives we own and theme — not a heavy component lib. |
| **Framer Motion** | Purposeful motion, shared layout transitions, gesture support. |
| **TanStack Query** | Server-state caching, optimistic updates, background refetch. |
| **Zustand** | Lightweight client/UI state (command palette, theme, ephemeral UI). |
| **React Hook Form + Zod** | Performant forms with one schema shared client↔server. |

## Backend

| Choice | Why |
|---|---|
| **Next.js Route Handlers + service layer** | One deployable; escalate to NestJS only via ADR. |
| **Prisma ORM** | Type-safe data access; migrations; client extensions for tenant guards. |
| **PostgreSQL** | Relational integrity, JSONB flexibility, `pgvector` for embeddings. |
| **Redis** | Sessions, caching, rate limiting, BullMQ broker. |
| **S3-compatible storage** | Media/files; works with AWS S3, Cloudflare R2, MinIO locally. |
| **BullMQ** | Reliable, observable background jobs with retries and scheduling. |

## Authentication

| Choice | Why |
|---|---|
| **Auth.js / Better Auth** | Pluggable providers, sessions, OAuth; extendable for enterprise SSO. |
| **SAML + LDAP** | Enterprise SSO/directory requirements from Phase 1. |
| **MFA (TOTP)** | Baseline account security; enforceable per-tenant policy. |

## AI

| Choice | Why |
|---|---|
| **Provider abstraction (Anthropic + OpenAI + optional xAI)** | Avoid lock-in; fallback + per-feature model selection. |
| **pgvector** | Keep vectors in Postgres → one datastore, tenant-scoped, transactional. |
| **RAG + embeddings** | Grounded tutor, semantic search, course-aware generation. |

> If a managed vector DB (e.g. for scale) becomes necessary, document it in an ADR before adopting.

## Tooling & infra

| Area | Choice |
|---|---|
| Package manager | **pnpm** (workspaces-ready, fast, strict) |
| Lint/format | **ESLint + Prettier** (typed lint rules) |
| Unit/integration tests | **Vitest** + Testing Library |
| E2E tests | **Playwright** |
| Validation | **Zod** everywhere at boundaries |
| Logging | structured (**pino**) |
| Containers | **Docker** + `docker-compose` |
| CI/CD | **GitHub Actions** |
| IaC | **Terraform-ready** layout (not required day one) |
| Observability | OpenTelemetry hooks + **Sentry** |

## Versioning policy

- Renovate/Dependabot-style updates; security patches promptly.
- Major upgrades go through an ADR + a dedicated `chore` PR with tests green.
