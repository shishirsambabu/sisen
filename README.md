# Aurora LMS

> An AI-native, multi-tenant enterprise Learning Experience Platform (LXP/LMS).
> Modern, premium, minimal, intelligent, fast. Built to compete with WorkRamp, 360Learning,
> Docebo, Degreed, and Cornerstone — with AI-first experiences they don't ship.

This repository is **build-ready for [Claude Code](https://www.anthropic.com/claude-code)**.
The planning kit (`CLAUDE.md`, `/docs`, `.claude/`) tells Claude Code exactly what to build,
in what order, and to what quality bar. Open it in Claude Code and run `/plan-phase 0` to begin.

---

## Quickstart (once Phase 0 is built)

```bash
# 1. Prereqs: Node 20+, pnpm, Docker
corepack enable

# 2. Install
pnpm install

# 3. Environment
cp .env.example .env.local   # fill in values; see .env.example for docs

# 4. Local infra (Postgres + Redis + S3-compatible storage)
docker compose up -d

# 5. Database
pnpm db:migrate
pnpm db:seed

# 6. Run
pnpm dev          # http://localhost:3000
```

## Useful commands

| Command | What it does |
|---|---|
| `pnpm dev` | Run the app locally |
| `pnpm build` / `pnpm start` | Production build / serve |
| `pnpm typecheck` | `tsc --noEmit` (strict) |
| `pnpm lint` / `pnpm format` | ESLint / Prettier |
| `pnpm test` | Unit + integration (Vitest) |
| `pnpm test:e2e` | End-to-end (Playwright) |
| `pnpm db:migrate` / `pnpm db:seed` / `pnpm db:studio` | Prisma workflows |

## How this repo is organized

- **`CLAUDE.md`** — master build instructions. Start here.
- **`docs/ROADMAP.md`** — all 20 phases + speedrun milestones + per-phase Definition of Done.
- **`docs/`** — architecture, tech stack, data model, design system, security, testing, workflow.
- **`docs/phases/`** — detailed specs for the first phases (0–4).
- **`claude-config/`** — Claude Code commands (`/plan-phase`, `/build-phase`, `/verify-phase`,
  `/ship-phase`, `/design-review`) and specialized subagents.
  **Rename this folder to `.claude/` after cloning** (`mv claude-config .claude`); see
  `claude-config/README.md`.

## Building with Claude Code

```text
/plan-phase 0     # produce the plan for the current phase
/build-phase 0    # implement it end-to-end
/verify-phase 0   # typecheck, lint, test, build, a11y/design check
/ship-phase 0     # changelog + conventional commit
```

Work proceeds one phase at a time. Claude Code stops at each phase boundary for approval unless
told to continue autonomously. See `docs/WORKFLOW.md`.

## Status

Pre-implementation. This is the planning kit; the application source tree is created in Phase 0.

## License

Proprietary — all rights reserved (update if you intend to open-source).
