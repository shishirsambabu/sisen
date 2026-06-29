# ADR 0001 — Record architecture decisions

- **Status:** Accepted
- **Date:** 2026-06-29

## Context

This project makes many significant technical choices (multi-tenancy strategy, AI provider
abstraction, vector store, when to split out a service, etc.). We need a lightweight, durable record
of *why* decisions were made so future contributors (human and AI) don't re-litigate or silently
reverse them.

## Decision

We use **Architecture Decision Records (ADRs)**. Each notable decision gets a numbered Markdown file
in `docs/adr/` using the template below. ADRs are required (per `CLAUDE.md`) before:

- adding a new major dependency,
- changing the multi-tenancy or data-isolation strategy,
- introducing a new infrastructure component or service boundary,
- choosing/replacing an AI provider, vector store, or auth mechanism.

## Consequences

- A small amount of overhead per significant decision.
- Clear, greppable history of architectural intent; easier onboarding and review.
- PRs link the ADRs they implement.

---

## ADR template

```md
# ADR NNNN — <title>

- **Status:** Proposed | Accepted | Superseded by ADR-XXXX
- **Date:** YYYY-MM-DD

## Context
What problem/forces are at play?

## Decision
What we chose and the key reasons.

## Alternatives considered
Options and why they were rejected.

## Consequences
Trade-offs, follow-ups, what this constrains or enables.
```
