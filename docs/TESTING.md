# Testing Strategy

> A phase is not done until its tests pass. Tests are part of the feature, written with it.

## Test pyramid

1. **Unit (Vitest)** — pure logic: services, policy/`can()`, validation schemas, utils, AI output
   parsers. Fast, isolated, no real DB. Aim for high coverage of domain logic.
2. **Integration (Vitest + test DB)** — service ↔ Prisma ↔ Postgres against a real (containerized)
   database. Covers data-access guards, migrations, transactions, and **tenant isolation**.
3. **End-to-end (Playwright)** — critical user journeys across the real app: sign-in/SSO, org
   onboarding, course create/consume, assessment attempt, AI tutor chat, certificate issue/verify.

## Mandatory test categories

- **Tenant isolation:** for every tenant-owned resource, prove tenant A cannot access tenant B's
  data via any route/handler. These tests gate merges.
- **AuthZ:** each role can do what it should and is denied what it shouldn't (policy module + routes).
- **Validation:** invalid inputs are rejected at the boundary with helpful errors.
- **AI paths:** provider abstraction is mocked in unit/integration; assert prompt construction,
  Zod-validated outputs, graceful fallback when a provider errors, and RAG retrieval is
  tenant-filtered. A small number of live-provider smoke tests may run behind a flag.
- **Accessibility:** automated checks (axe) in component/E2E tests for new screens; plus the manual
  checklist below.
- **Visual/states:** loading, empty, error, success states are exercised.

## Coverage gates (CI)

- Domain/service code: meaningful coverage (target ≥ 80% lines on `src/modules/**/service` and
  `src/server/**`). Don't chase 100% on UI glue.
- No merge with failing `typecheck`, `lint`, `test`, critical `test:e2e`, or `build`.

## Accessibility checklist (per new screen)

- [ ] Fully keyboard operable; visible focus; logical tab order.
- [ ] Labels/roles correct; errors announced (live regions).
- [ ] Contrast ≥ WCAG AA; not color-alone.
- [ ] Respects `prefers-reduced-motion`.
- [ ] Works mobile → ultrawide.

## Tooling & conventions

- `pnpm test` (unit+integration), `pnpm test:e2e` (Playwright), `--coverage` in CI.
- Test data via factories + the seed system; integration tests run against an ephemeral DB
  (docker-compose service or Testcontainers) and reset between suites.
- Name tests by behavior ("denies cross-tenant read"), not implementation.
- Every bug fix adds a regression test.

## CI pipeline (GitHub Actions)

`install → typecheck → lint → unit+integration (with test DB) → build → e2e (critical) → coverage`.
Required to be green before merge to `main`. Secrets via GitHub encrypted secrets; never echoed.
