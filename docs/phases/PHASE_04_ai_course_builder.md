# Phase 4 — AI Course Builder

> Goal: from a single prompt ("Create an Excel course"), generate a complete, **editable** course.
> This is a flagship AI feature. It composes the Phase 3 course model, not a parallel one.

## Scope

Given a prompt (+ optional constraints: audience, level, duration, target skills), generate:
- **Modules** and **lessons** with learning **objectives**.
- **Assignments** and **activities**.
- **Flashcards**, **quizzes** (typed questions, ties into Phase 7), **summaries**.
- **Estimated duration**, **skill mappings**, **difficulty**, **prerequisites**.

Everything is produced as **editable drafts** in the Phase 3 builder — the author reviews, edits,
regenerates sections, and publishes. Nothing is auto-published.

## How it works
1. User prompt + options → validated (Zod).
2. **Provider abstraction** generates a structured outline via `generateObject` against a strict Zod
   schema (modules → lessons → objectives → activities → assessments → metadata). Invalid output is
   rejected/retried — never persisted unvalidated.
3. Long generations run as **BullMQ jobs** with streamed progress to the UI.
4. Output maps to real `Course/Module/Lesson/...` draft records (tenant-scoped, audit-logged).
5. Author edits inline (Phase 3 builder); per-section **regenerate** and **expand** actions.
6. Optional grounding: generate from existing org content via RAG (reuse Phase 8 retrieval).

## Guardrails
- Generated content is clearly marked AI-generated until reviewed.
- Cost/usage metered per tenant; rate-limited.
- No PII to providers without policy check; outputs validated and sanitized before render.
- Graceful fallback across providers; clear error + retry UX if generation fails.

## Key entities (extend from `DATA_MODEL.md`)
`GenerationJob` (status, prompt, options, cost, result ref), `GeneratedOutline` (editable draft
graph) → materialized into Phase 3 `Course/Module/Lesson` drafts.

## UI surfaces
"Generate a course" entry (prompt + options), streaming generation view with progress, draft review
in the builder with section-level regenerate/edit, publish flow. Beautiful loading + AI affordances.

## Tests (must pass)
- Prompt → schema-valid outline (provider mocked); invalid model output is rejected, not stored.
- Generation job lifecycle: queued → running → completed/failed, progress events emitted.
- Generated drafts are tenant-scoped, editable, and only persisted as drafts; publish is explicit.
- Regenerate-section replaces only the targeted node.
- Metering records usage per tenant; fallback path covered.

## Definition of Done
Shared DoD + "a prompt produces a complete, editable, schema-valid course draft in the builder,
generated via the provider abstraction with metered, tenant-scoped, validated outputs."
