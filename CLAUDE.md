# CLAUDE.md — Master Build Instructions

> This is the single source of truth for how Claude Code builds this project.
> Read this file at the start of **every** session. When in doubt, this file wins.
> Detailed specs live in `/docs`. This file tells you how to think, what order to
> work in, and what "done" means.

---

## 0. What we are building

**Product:** An AI-native, multi-tenant enterprise Learning Experience Platform (LXP/LMS).

**Codename:** `aurora-lms` (change in `package.json` if you prefer another name).

**One-line vision:** The Linear + Notion + Stripe + Vercel + Cursor of enterprise learning —
modern, premium, minimal, intelligent, and fast. Not a traditional LMS.

**Competitive frame:** Should be credible against WorkRamp, 360Learning, Docebo, LearnUpon,
Degreed, Cornerstone, TalentLMS, and an enterprise Moodle replacement — while delivering
AI-first experiences none of them ship today.

Full feature scope is in [`docs/ROADMAP.md`](docs/ROADMAP.md) (Phases 0–19).

---

## 1. Non-negotiable principles

1. **AI-first.** For every feature, ask "can AI make this dramatically better?" If yes, build the
   AI path as part of the feature — never bolt it on later. AI must degrade gracefully when a
   provider is down or a key is missing.
2. **Enterprise-first.** Multi-tenancy, RBAC, auditability, and tenant isolation are designed in
   from Phase 0, not retrofitted. Every query is tenant-scoped. Every mutation is audit-logged.
3. **Production quality only.** No demos, no fake APIs, no placeholder logic. Mock data is allowed
   **only** through Phase 2 and only behind the seed system; after that, real data paths.
4. **Premium UX.** Every screen ships with loading, empty, error, and success states. Motion
   communicates state. WCAG 2.1 AA minimum. See [`docs/DESIGN_SYSTEM.md`](docs/DESIGN_SYSTEM.md).
5. **Strict typing & validation.** `strict: true` TypeScript. Every external input is validated
   with Zod at the boundary. No `any` without a written justification comment.
6. **Tested.** Unit + integration + E2E. A phase is not done until its tests pass. See
   [`docs/TESTING.md`](docs/TESTING.md).

If a request ever conflicts with these principles, stop and flag it rather than silently
compromising.

---

## 2. Tech stack (authoritative)

Use these unless a documented ADR overrides them. Pin to the **latest stable** at init time and
record the versions in `package.json`. Full rationale: [`docs/TECH_STACK.md`](docs/TECH_STACK.md).

**Frontend:** Next.js (App Router) · React · TypeScript (strict) · Tailwind CSS · shadcn/ui ·
Framer Motion · TanStack Query · Zustand · React Hook Form · Zod.

**Backend:** Next.js Route Handlers (escalate to a dedicated NestJS service only when justified by
an ADR) · Prisma ORM · PostgreSQL · Redis · S3-compatible object storage · BullMQ for queues.

**Auth:** Auth.js / Better Auth · email+password · Google · Microsoft · MFA · SAML · LDAP.

**AI:** Provider abstraction over Anthropic + OpenAI (+ optional xAI) · embeddings · RAG ·
pgvector (default vector store; revisit only via ADR).

**Infra:** Docker · docker-compose for local · GitHub Actions CI/CD · environment validation ·
structured logging · observability hooks · Terraform-ready layout.

> **Do not** introduce a new major dependency without (a) checking it is actively maintained and
> (b) writing a one-paragraph ADR in `docs/adr/`.

---

## 3. Repository layout

```
.
├── CLAUDE.md                 # you are here
├── README.md                 # human-facing overview + quickstart
├── CONTRIBUTING.md           # workflow, commit, branch, PR rules
├── CHANGELOG.md              # keep-a-changelog format, updated every phase
├── .env.example              # every env var, documented, no secrets
├── .gitignore
├── docs/
│   ├── ROADMAP.md            # all 20 phases + speedrun milestones
│   ├── ARCHITECTURE.md       # system design, boundaries, data flow
│   ├── TECH_STACK.md         # stack choices + rationale
│   ├── DATA_MODEL.md         # Prisma schema design, multi-tenancy strategy
│   ├── DESIGN_SYSTEM.md      # tokens, components, motion, a11y
│   ├── SECURITY.md           # authz, isolation, secrets, compliance posture
│   ├── TESTING.md            # test strategy, coverage gates, tools
│   ├── WORKFLOW.md           # the per-phase loop Claude Code must follow
│   ├── adr/                  # architecture decision records
│   └── phases/               # one detailed spec per early phase
│       ├── PHASE_00_foundation.md
│       ├── PHASE_01_auth_orgs.md
│       ├── PHASE_02_experience.md
│       ├── PHASE_03_course_builder.md
│       └── PHASE_04_ai_course_builder.md
└── .claude/
    ├── settings.json         # project Claude Code settings
    ├── commands/             # /plan-phase, /build-phase, /verify-phase, /ship-phase, /design-review
    └── agents/               # architect, fullstack-builder, test-engineer, security-reviewer, design-reviewer
```

The application source tree (created in Phase 0) is documented in
[`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).

---

## 4. How to work: the phase loop

Build **one phase at a time, in order.** Phases and their Definition of Done are in
[`docs/ROADMAP.md`](docs/ROADMAP.md); the detailed loop is in
[`docs/WORKFLOW.md`](docs/WORKFLOW.md). The short version, runnable via slash commands:

1. **`/plan-phase N`** — produce an implementation plan; write/refresh the phase spec and any ADRs.
2. **`/build-phase N`** — implement the feature end-to-end (schema → API → UI → AI → states).
3. Add **tests** (unit + integration + E2E for critical paths).
4. **`/verify-phase N`** — typecheck, lint, run all test suites, build, a11y + design check.
5. Update **docs** and **CHANGELOG.md**.
6. **`/ship-phase N`** — conventional commit, summary of what changed and what's next.
7. **Wait for approval** before starting the next phase — unless the user said "continue
   autonomously," in which case proceed but still stop on any failing gate.

**Never** mark a phase done with failing tests, type errors, lint errors, or unaddressed
security/a11y items. If blocked, say so and propose options instead of faking completion.

---

## 5. Definition of Done (every phase)

A phase merges only when **all** of these hold:

- [ ] All planned features for the phase are implemented and wired to real data paths.
- [ ] `pnpm typecheck` passes with zero errors (strict mode).
- [ ] `pnpm lint` passes with zero errors.
- [ ] `pnpm test` (unit + integration) and E2E for critical flows pass.
- [ ] `pnpm build` succeeds.
- [ ] UI matches the design system; loading/empty/error/success states exist.
- [ ] WCAG 2.1 AA checks pass for new screens (keyboard, contrast, labels, focus).
- [ ] Multi-tenancy respected: every new query is tenant-scoped; isolation tested.
- [ ] New mutations are audit-logged; new inputs are Zod-validated.
- [ ] Docs + `CHANGELOG.md` updated; ADRs written for notable decisions.
- [ ] Conventional commit made.

---

## 6. Coding standards (summary)

- **Modular, DRY, composition over inheritance, SOLID where it applies.** Small, testable functions.
- **Strict TypeScript.** No implicit `any`; validate all external inputs with Zod at the edge.
- **Errors handled gracefully** with typed results and structured logging — never swallow errors.
- **Tenant scoping is mandatory** on every data access. Prefer a guarded data-access layer over
  raw Prisma calls in route handlers.
- **Performance:** code-splitting, lazy loading, caching, no needless client components, no N+1
  queries. Server Components by default; client only when interaction requires it.
- **Accessibility from the start**, not as a cleanup pass.
- **Secrets** only via env (validated). Never commit secrets; never log them.
- Document complex modules with a short header comment explaining the "why."

Full detail in [`docs/TESTING.md`](docs/TESTING.md), [`docs/SECURITY.md`](docs/SECURITY.md), and
[`docs/DESIGN_SYSTEM.md`](docs/DESIGN_SYSTEM.md).

---

## 7. Commands you can assume exist (define in Phase 0)

```
pnpm dev          # run app locally
pnpm build        # production build
pnpm start        # run production build
pnpm typecheck    # tsc --noEmit
pnpm lint         # eslint
pnpm format       # prettier --write
pnpm test         # unit + integration (Vitest)
pnpm test:e2e     # Playwright
pnpm db:migrate   # prisma migrate
pnpm db:seed      # seed system
pnpm db:studio    # prisma studio
docker compose up # local Postgres + Redis + storage
```

Wire these in Phase 0. If you change them, update this list and the README.

---

## 8. Guardrails

- Don't skip phases or build ahead of the current one without being asked.
- Don't introduce new infra/deps without an ADR.
- Don't weaken tenant isolation, auth, or validation for convenience.
- Don't leave TODOs in shipped code without a tracked follow-up in the phase doc.
- When uncertain about product scope, prefer the behavior described in `docs/ROADMAP.md` and the
  relevant `docs/phases/*` spec; if still ambiguous, ask.

Build like this is going to production for a real enterprise customer — because the spec says it is.
