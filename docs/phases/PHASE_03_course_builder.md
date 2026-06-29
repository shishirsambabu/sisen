# Phase 3 — Course Builder

> Goal: the best course builder in the market — flexible, fast, premium, and standards-compliant.
> Real data only (post Phase 2). This is the content backbone the AI features build on.

## Scope

### Content authoring (block-based)
- Rich text, images, **tables**, **code blocks** (syntax highlight), **callouts**, dividers, embeds.
- Drag-and-drop block ordering; nested structure: `Course → Module → Lesson → ContentBlock`.
- Autosave, version/draft vs published, optimistic edits, undo.

### Media
- **Video:** upload + YouTube + Vimeo + **interactive video**; **transcripts**; **AI subtitles**
  (job-based, see AI). Chapter markers / timestamps.
- **Audio** upload + player.
- **Documents:** PDF, DOCX, PPTX, Excel — viewer + extraction for search/RAG later.
- All media via storage abstraction; processing via BullMQ jobs (transcode/thumbnail/transcript).

### Standards
- **SCORM 1.2** and **SCORM 2004** import/playback + progress/score tracking.
- **xAPI** statements (LRS-style) for tracked activities.

### Assignments & labs
- Assignments with rubrics; **portfolio submissions**; **GitHub link** submissions.
- **Coding labs:** browser VS Code (e.g. code-server) backed by **Docker containers**, sandboxed,
  resource-limited, tenant-isolated. (Document the isolation model in an ADR.)

## AI opportunities (built in)
- **AI subtitles/transcripts** generation pipeline.
- Inline authoring copilot: improve text, generate a callout, draft a code example, suggest a quiz
  for a lesson (ties into Phase 4/7).
- Content embedding on publish → feeds RAG (Phase 8) and AI search (Phase 17).

## Key entities (extend from `DATA_MODEL.md`)
`Course`, `Module`, `Lesson`, `ContentBlock`, `MediaAsset`, `Transcript`, `ScormPackage`,
`Assignment`, `Submission`, plus xAPI `Statement` records.

## UI surfaces
Course builder canvas (block editor + outline), media manager, SCORM import, assignment/lab config,
learner consumption view (player + progress). Premium, keyboard-friendly, all four states, smooth DnD.

## Tests (must pass)
- CRUD for course/module/lesson/block, tenant-scoped + audit-logged; ordering persists.
- Media upload → job → playable asset; transcript/subtitle job produces output (provider mocked).
- SCORM 1.2 + 2004 packages import and report progress/score; xAPI statements recorded.
- Coding lab container spins up sandboxed and tenant-isolated; cleanup on session end.
- Learner can consume a course and progress is tracked.

## Definition of Done
Shared DoD + "an instructor can author a rich, standards-compliant course with media, SCORM/xAPI,
and labs; learners can consume it with progress tracking — all tenant-scoped and tested."
