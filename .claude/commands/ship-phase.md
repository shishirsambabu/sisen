---
description: Finalize a phase — docs, changelog, conventional commit
argument-hint: <phase-number>
---

You are shipping **Phase $1** of Aurora LMS. Only run this after `/verify-phase $1` is fully green.

Do this:

1. Confirm the Definition of Done checklist (`CLAUDE.md` §5) is fully satisfied. If not, stop and
   say what's missing.
2. **Update docs** affected by this phase (`ARCHITECTURE.md`, `DATA_MODEL.md`, `DESIGN_SYSTEM.md`,
   the phase spec's "what shipped / deferred" notes, and any ADRs).
3. **Update `CHANGELOG.md`** under a new `## [0.$1.0] — Phase $1: <name> — <date>` section using
   Keep a Changelog categories (Added/Changed/Fixed/Security).
4. **Commit** with Conventional Commits, e.g.
   `feat(phase-$1): <summary>`. Group logically if multiple commits make sense.
   Do **not** push unless the user asks.
5. Write a short **handoff summary**: what shipped, what was deferred (with tracked follow-ups in the
   phase doc), and what the next phase is.
6. **Stop and wait for approval** before starting the next phase — unless the user said to continue
   autonomously, in which case proceed to `/plan-phase <next>` but still stop on any failing gate.
