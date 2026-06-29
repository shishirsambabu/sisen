# Phase 0 — Foundation

> Goal: a production-grade skeleton. Everything else is built on this, so do it right.
> No product features yet — but the architecture, guards, and tooling that make every later phase
> fast and safe. **Output:** an app that installs, builds, lints, typechecks, tests, and boots
> locally with Postgres + Redis + storage via docker-compose.

## Scope

1. **Project init**
   - Next.js (App Router) + TypeScript **strict** + pnpm.
   - Source tree per `ARCHITECTURE.md` §3 (`src/app`, `src/modules`, `src/server`, `src/ui`,
     `src/lib`, `prisma`, `tests`).
2. **Quality tooling**
   - ESLint (typed rules) + Prettier; `pnpm lint`, `pnpm format`.
   - Vitest (unit/integration) + Playwright (E2E); `pnpm test`, `pnpm test:e2e`.
   - `pnpm typecheck` (`tsc --noEmit`).
   - Git hooks (lint-staged + commit message check) — optional but recommended.
3. **Environment validation** — `src/env.ts` parses `process.env` with Zod; app refuses to boot on
   missing/invalid required vars. Mirror every var in `.env.example`.
4. **Logging & observability** — structured logger (pino) with request/tenant/actor correlation;
   health/readiness endpoints; Sentry + OpenTelemetry hooks (no-op if unconfigured).
5. **Feature flags** — `src/server/flags` with a default source + DB-backed per-tenant overrides;
   simple `isEnabled(flag, ctx)` API.
6. **RBAC foundation** — role enum + permission catalog + central `can(actor, action, resource)`
   policy module (no business resources yet, but the mechanism + tests exist).
7. **Multi-tenancy foundation** — `TenantContext` type; Prisma client extension that injects/
   enforces `organizationId` on tenant-owned models; un-scoped queries throw. This is the spine —
   get it solid and tested before any tenant data exists.
8. **Database** — Prisma schema with the Phase 0–2 core entities from `DATA_MODEL.md`
   (Organization, User, Membership, Role, Department, Team, AuthAccount, Session, AuditLog,
   Notification, FeatureFlag). First migration. `pnpm db:migrate`, `db:studio`.
9. **Storage abstraction** — `src/server/storage` over S3-compatible (MinIO locally); signed URLs;
   tenant-namespaced keys.
10. **Email/notification abstraction** — `src/server/email` + `Notification` model; pluggable
    provider (SMTP/Resend/etc.); templated, no-op-safe in dev.
11. **Audit logging** — `src/server/audit` append-only writer used by the service layer.
12. **Seed system** — idempotent, environment-aware `prisma/seed.ts`: demo org, roles/permissions,
    one user per role. Never seeds demo data into production.
13. **Containers & CI/CD**
    - `Dockerfile` (multi-stage) + `docker-compose.yml` (app, Postgres, Redis, MinIO).
    - GitHub Actions: `install → typecheck → lint → unit+integration (test DB) → build → e2e`.
    - Terraform-ready layout (a `infra/` placeholder is fine).
14. **Scripts** — wire every command in `CLAUDE.md` §7.

## Out of scope (later phases)
Real auth providers/SSO (Phase 1 builds on the foundation), product UI, AI features.

## Key files produced
`package.json`, `tsconfig.json`, `.eslintrc`, `prettier` config, `vitest.config.ts`,
`playwright.config.ts`, `src/env.ts`, `prisma/schema.prisma`, `prisma/seed.ts`,
`src/server/{db,auth,storage,email,audit,flags,observability}/*`, `src/lib/policy`,
`Dockerfile`, `docker-compose.yml`, `.github/workflows/ci.yml`, health route.

## Tests (must pass)
- Env validation rejects missing required vars.
- **Tenant guard:** a query on a tenant-owned model without `organizationId` throws; a scoped query
  succeeds; tenant A cannot read tenant B (seed two orgs).
- Policy `can()` returns expected allow/deny for sample role/action pairs.
- Audit writer appends a record for a sample mutation.
- App boots; health endpoint returns OK with DB/Redis/storage checks.

## Definition of Done
Shared DoD (`CLAUDE.md` §5) + the deliverable above: `pnpm install && pnpm typecheck && pnpm lint
&& pnpm test && pnpm build` all green, `docker compose up` brings up the stack, `db:migrate` +
`db:seed` work, and the tenant guard is proven by tests.
