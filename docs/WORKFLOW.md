# Development Workflow

> The exact loop Claude Code follows for every phase. Mirrors `CLAUDE.md` §4 with detail.
> Slash commands in `.claude/commands/` automate each step.

## The phase loop

### 1. Plan — `/plan-phase N`
- Read `CLAUDE.md`, `docs/ROADMAP.md`, and the phase spec (`docs/phases/PHASE_0N_*.md` if present).
- Produce a concrete implementation plan: schema changes, modules/files, API surface, UI screens,
  AI touchpoints, tests, and risks.
- Write/refresh the phase spec; open ADRs for any notable decisions or new dependencies.
- **Output:** a checked plan in the phase doc. Pause for approval unless told to continue.

### 2. Build — `/build-phase N`
Implement end-to-end, bottom-up within the slice:
1. **Schema & migration** (Prisma) + tenant scoping + indexes.
2. **Service layer** (domain logic, `can()` authz, audit logging) with unit tests.
3. **API boundary** (route handlers/server actions) with Zod validation + integration tests.
4. **AI path** where the feature benefits (provider abstraction, RAG, jobs) — built in, not bolted on.
5. **UI** from the design system: all four states, motion, a11y, keyboard, responsive.
6. **Seeds/flags** as needed (seed/mock data only allowed through Phase 2).

### 3. Verify — `/verify-phase N`
Run the full gate and fix until green:
```
pnpm typecheck && pnpm lint && pnpm test && pnpm build && pnpm test:e2e
```
Plus: accessibility checklist, design-system conformance, tenant-isolation tests, performance
sanity (no N+1, pagination, indexes). Use the `security-reviewer` and `design-reviewer` subagents.

### 4. Document
- Update affected docs (`ARCHITECTURE`, `DATA_MODEL`, `DESIGN_SYSTEM`, etc.).
- Update `CHANGELOG.md` (Keep a Changelog format).
- Ensure the phase's Definition of Done checklist is fully checked.

### 5. Ship — `/ship-phase N`
- Conventional commit(s) on the `phase/NN-name` branch.
- Summary: what shipped, what's deferred (with tracked follow-ups), what's next.
- **Stop for approval** before the next phase unless the user said "continue autonomously."
  Even autonomously, **never** proceed past a failing gate.

## Working agreements

- One phase at a time, in order. No building ahead without being asked.
- Prefer many small, reviewable commits over one large one.
- If blocked or a requirement is ambiguous, stop and present options — don't fake completion or
  silently weaken a principle (tenancy, authz, validation, a11y).
- Keep `CLAUDE.md` §7 command list accurate if scripts change.

## Speedrun cadence

`ROADMAP.md` groups phases into weekly speedruns. Treat each speedrun as a milestone: at its end,
the grouped phases are all "Done" per the checklist and `main` is green and demoable.
