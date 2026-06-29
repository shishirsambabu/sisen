---
description: Produce an implementation plan for a phase before building it
argument-hint: <phase-number>
---

You are planning **Phase $1** of Aurora LMS.

Do this:

1. Read `CLAUDE.md`, `docs/ROADMAP.md`, and the phase spec
   `docs/phases/PHASE_0$1_*.md` if it exists (for later phases, use the ROADMAP entry).
2. Read any docs the phase touches: `ARCHITECTURE.md`, `DATA_MODEL.md`, `DESIGN_SYSTEM.md`,
   `SECURITY.md`, `TESTING.md`.
3. Produce a concrete plan covering:
   - Prisma **schema changes** (models, fields, indexes, tenant scoping) + migration.
   - **Modules/files** to add or change (use the source tree in `ARCHITECTURE.md`).
   - **Service-layer** logic + authorization (`can()`) + audit points.
   - **API boundary** (route handlers / server actions) + Zod schemas.
   - **AI touchpoints** (provider abstraction, RAG, jobs) — built in, not bolted on.
   - **UI screens** + required states (loading/empty/error/success) + a11y + keyboard.
   - **Tests** (unit, integration incl. tenant isolation, E2E for critical flows).
   - **Risks / open questions** and any **ADRs** needed (new deps, schema strategy, providers).
4. If a detailed spec file doesn't exist for this phase, **write one** at
   `docs/phases/PHASE_<NN>_<name>.md` following the structure of the existing Phase 0–4 specs.
5. Present the plan as a checklist. **Stop and wait for approval** unless the user told you to
   continue autonomously.

Do not write feature code in this step. Planning and spec/ADR docs only.
