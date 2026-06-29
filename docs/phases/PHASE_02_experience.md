# Phase 2 — User Experience Platform

> Goal: a complete, personalized core user experience — the surface learners live in daily.
> **This is the last phase where mock/seed data is permitted.** After Phase 2, real data paths only.

## Scope

### Profiles
- Skills, interests, department, manager, designation, learning history, certifications.
- Editable profile with avatar upload (storage abstraction), privacy-aware fields.
- Manager relationships and team membership reflected.

### Personalized dashboard (home)
- **Continue learning** (resume in-progress), **recommendations** (role/skill/history-driven),
  **deadlines**, **achievements**, **learning calendar**, **notifications** feed.
- Daily learning goals, streaks, motivation nudges, career/skill visualization (EX principles).
- Built from real (seeded for now) data; recommendation logic abstracted so Phase 6/8 can enrich it.

### Navigation & search
- App shell: responsive sidebar + top bar + breadcrumbs (mobile → ultrawide).
- **Command palette** (`⌘K`): navigate, search, run actions, AI actions.
- **Global / spotlight search** across users, courses (stubbed until Phase 3), skills, pages —
  designed to plug into AI semantic search in Phase 17.
- Notifications center; keyboard shortcuts + help dialog; context menus / right-click.

## AI opportunities
- Recommendation engine (content-based now; embeddings-enhanced later).
- Command-palette AI actions ("summarize my week", "what's due", "suggest what to learn next").
- Personalized nudge copy generated within wellness-aware cadence (non-intrusive).

## Key entities (extend from `DATA_MODEL.md`)
`Profile`, `Notification`, plus read models for dashboard widgets (recommendations, deadlines,
achievements, calendar events).

## UI surfaces
Home dashboard, profile view/edit, notifications center, command palette, search results, app shell.
Every widget has skeleton/empty/error states; zero layout shift; animated charts; optimistic updates.

## Tests (must pass)
- Dashboard widgets render per-user, tenant-scoped, with correct empty/loading states.
- Command palette: keyboard nav, action execution, search filtering.
- Search returns only tenant-visible, permission-filtered results.
- Profile edit validates (Zod), persists, and audit-logs changes; avatar upload via storage.
- Accessibility: app shell + palette + dashboard pass keyboard + axe checks.

## Definition of Done
Shared DoD + "a learner lands on a personalized, fast, accessible home, can navigate and search the
whole app via keyboard and command palette, and manage their profile — all tenant-scoped."
After this phase, remove reliance on mock data going forward.
