# Contributing

This project is built primarily by Claude Code following `CLAUDE.md` and `docs/WORKFLOW.md`.
These rules apply to both AI and human contributors.

## Branching

- `main` — always green (builds, typechecks, tests pass). Protected.
- `phase/NN-short-name` — one branch per phase (e.g. `phase/00-foundation`).
- `feat/short-name`, `fix/short-name`, `chore/short-name` — for sub-tasks within a phase.

## Commits — Conventional Commits

```
<type>(<scope>): <subject>

[body: what & why, not how]
[footer: BREAKING CHANGE / refs]
```

Types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`, `build`, `ci`, `style`.
Scopes are usually the phase or module, e.g. `feat(auth): add SAML SSO flow`.

Keep commits focused and reversible. One logical change per commit.

## Pull requests

Every PR must:

1. Reference the phase and the items it completes.
2. Pass CI: `typecheck`, `lint`, `test`, `test:e2e` (critical paths), `build`.
3. Include/extend tests for new behavior.
4. Update `CHANGELOG.md` and any affected docs.
5. Confirm the Definition of Done checklist from `CLAUDE.md` §5.

## Code review checklist

- Tenant scoping present on every new data access?
- Inputs validated with Zod at the boundary?
- Mutations audit-logged?
- Loading / empty / error / success states present?
- Accessibility: keyboard, focus, labels, contrast?
- No secrets, no `any` without justification, no swallowed errors?
- Performance: no N+1, appropriate Server vs Client components, caching considered?

## Definition of Done

See `CLAUDE.md` §5. A change is not done until all gates pass — no exceptions for "we'll fix it later."

## Architecture decisions

Anything notable (new dependency, schema strategy, provider choice) gets a short ADR in
`docs/adr/` using the template there. Link the ADR from the PR.
