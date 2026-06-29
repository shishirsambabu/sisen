---
description: Run the full quality gate for a phase and fix until green
argument-hint: <phase-number>
---

You are verifying **Phase $1** of Aurora LMS against the Definition of Done (`CLAUDE.md` §5).

Run the gate and fix issues until everything is green:

```bash
pnpm typecheck
pnpm lint
pnpm test          # unit + integration (incl. tenant-isolation tests)
pnpm build
pnpm test:e2e      # critical flows
```

Then check, and fix any gaps:

- **Tenant isolation:** new resources are scoped; isolation tests exist and pass.
- **AuthZ:** new mutations go through `can()`; covered by tests.
- **Validation:** all new inputs Zod-validated at the boundary.
- **Audit:** new mutations are audit-logged.
- **Design system:** tokens only, all four states, motion, responsive.
- **Accessibility:** run the checklist in `docs/TESTING.md` (keyboard, focus, labels, contrast,
  reduced motion) + axe checks on new screens. Use the `design-reviewer` subagent.
- **Security:** use the `security-reviewer` subagent on the diff.
- **Performance:** no N+1, lists paginated, indexes present, Server vs Client components sensible.

Report a pass/fail summary against the DoD checklist. If anything fails, fix it (or, if it needs a
decision, present options). Do not declare the phase done with any failing gate.

When everything passes, tell the user to run `/ship-phase $1`.
