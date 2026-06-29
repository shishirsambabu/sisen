---
description: Implement a phase end-to-end following the approved plan
argument-hint: <phase-number>
---

You are building **Phase $1** of Aurora LMS. Follow the approved plan and `docs/WORKFLOW.md`.

Create a branch `phase/$1-<short-name>` if not already on one.

Implement the slice **bottom-up**, committing in small logical steps:

1. **Schema & migration** — Prisma models with `organizationId` + indexes; run `pnpm db:migrate`.
2. **Service layer** — domain logic receiving `TenantContext`; authorize via `can()`; audit-log
   mutations. Add **unit tests** as you go.
3. **API boundary** — route handlers / server actions; **Zod-validate every input**; rate-limit
   sensitive endpoints. Add **integration tests**, including **tenant-isolation** tests.
4. **AI path** — where the feature benefits, use the provider abstraction (`generateObject` with
   strict Zod schemas), RAG retrieval (tenant-filtered), and BullMQ jobs with progress. Mock
   providers in tests; handle fallback + errors gracefully.
5. **UI** — build from the design system (`docs/DESIGN_SYSTEM.md`): tokens only, all four states
   (loading/empty/error/success), purposeful motion, full keyboard support, WCAG AA, responsive.
6. **Seeds/flags** — only add seed/mock data if this is Phase 0–2; otherwise real data paths.

Rules:
- Strict TypeScript, no unjustified `any`, no swallowed errors, no secrets in code/logs.
- Every tenant data access is scoped; every mutation is audit-logged and authorized.
- Don't build beyond Phase $1's scope.

When the feature is implemented and tests are written, tell the user to run `/verify-phase $1`.
