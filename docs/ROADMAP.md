# Roadmap — Phases 0–19

> Build order is authoritative. One phase at a time (see `WORKFLOW.md`).
> Detailed specs for early phases live in `docs/phases/`. Each phase ends with the shared
> Definition of Done (`CLAUDE.md` §5) **plus** any phase-specific deliverable listed here.

## Speedrun milestones

| Speedrun | Week | Phases | Outcome |
|---|---|---|---|
| 1 | 1 | 0, 1 | Architecture + auth + multi-tenancy + org onboarding |
| 2 | 2 | 2 | Dashboard, profiles, navigation, search, command palette |
| 3 | 3 | 3 | Course builder, media uploads, SCORM/xAPI |
| 4 | 4 | 4, (7 quiz gen, 8 tutor MVP) | AI course generator, AI quiz generator, AI tutor |
| 5 | 5 | 7, 9, 5 | Assessments, certifications, learning paths |
| 6 | 6 | 6 | Skills platform + skill-gap AI |
| 7 | 7 | 13, 14 | Analytics + compliance |
| 8 | 8 | 16, 15, 19 | Mobile, integrations, production hardening |

> Phases 10 (gamification), 11 (social), 12 (live), 17 (AI search), 18 (enterprise admin) are
> sequenced after their dependencies; slot them into the speedrun where they unblock the most value.
> Don't reorder without an ADR.

---

## Phase 0 — Foundation  → `docs/phases/PHASE_00_foundation.md`
Production-grade skeleton: project init, lint/format, testing, logging, monitoring hooks, env
validation, feature flags, RBAC foundation, multi-tenancy foundation, Prisma schema + migrations,
Docker, CI/CD, storage abstraction, email/notification abstraction, audit logging, seed system.
**Deliverable:** a production-ready skeleton that builds, lints, tests, and boots locally.

## Phase 1 — Authentication & Organizations  → `docs/phases/PHASE_01_auth_orgs.md`
Email/password, Google, Microsoft, MFA, SAML, LDAP. Roles (Super Admin → Guest). Org multi-tenancy,
branding/logo/domain, email templates, user directory, policies, tenant + storage isolation.
**Deliverable:** organizations can fully onboard.

## Phase 2 — User Experience Platform  → `docs/phases/PHASE_02_experience.md`
Profiles (skills, interests, department, manager, designation, learning history, certifications).
Personalized dashboard: continue learning, recommendations, deadlines, achievements, learning
calendar, notifications. Command palette + global/spotlight search.
**Deliverable:** complete core user experience. *(Last phase where mock/seed data is permitted.)*

## Phase 3 — Course Builder  → `docs/phases/PHASE_03_course_builder.md`
Rich content blocks (text, images, tables, code, callouts), video (YouTube/Vimeo/interactive,
transcripts, AI subtitles), audio, documents (PDF/DOCX/PPTX/Excel), SCORM 1.2 & 2004, xAPI.
Assignments: coding labs (browser VS Code, Docker containers), portfolio submissions, GitHub links.
**Deliverable:** best-in-market course builder.

## Phase 4 — AI Course Builder  → `docs/phases/PHASE_04_ai_course_builder.md`
Prompt → generated modules, lessons, objectives, assignments, activities, flashcards, quizzes,
summaries, estimated duration, skill mappings, difficulty, prerequisites. Everything editable.
**Deliverable:** generate a full, editable course from a prompt.

## Phase 5 — Learning Paths
Visual drag-and-drop path builder. Conditional paths; role/department-based; manager-assigned;
auto-enrollment; recommendations; AI-generated paths.
**Deliverable:** authorable, conditional learning paths with enrollment.

## Phase 6 — Skills Platform
Enterprise skill taxonomy, competencies, proficiency levels, role mapping, career ladders, skill
matrices, gap analysis, AI recommendations.
**Deliverable:** skills graph + AI skill-gap analysis driving recommendations.

## Phase 7 — Assessments
Question types: MCQ, multi-select, essay, coding, fill-blank, match, ordering, case study, audio,
video. Randomization, difficulty, question bank, negative marking, analytics.
**Deliverable:** full assessment engine with grading + analytics.

## Phase 8 — AI Learning Assistant
ChatGPT-like tutor: explain, summarize, translate, generate examples/practice questions/flashcards,
mock interviews, roleplay, voice mode, lesson/PDF/video chat, code explanations, personal coaching.
RAG-grounded, tenant-scoped, streaming.
**Deliverable:** grounded AI tutor across content.

## Phase 9 — Certifications
Certificates with QR validation + public verification page, expiry/renewal, digital badges
(Open Badge compatible).
**Deliverable:** issue, verify, expire, and renew credentials.

## Phase 10 — Gamification
XP, coins, badges, leaderboards, challenges, missions, rewards, streaks, levels, achievement engine.
**Deliverable:** configurable gamification driving engagement.

## Phase 11 — Social Learning
Communities, forums, peer discussions, comments, mentions, likes, study groups, instructor Q&A,
knowledge sharing.
**Deliverable:** social layer with moderation + notifications.

## Phase 12 — Live Learning
Zoom/Meet/Teams integration, attendance, polling, whiteboards, breakout rooms, recordings,
calendar sync.
**Deliverable:** schedule, run, and record live sessions.

## Phase 13 — Analytics
Dashboards (org/department/course/skill/manager), completion, engagement, ROI, heatmaps, funnels.
Exports: CSV, Excel, PDF.
**Deliverable:** trustworthy analytics with exports.

## Phase 14 — Compliance
Mandatory learning, policies, escalations, reminders, audit trails, expiry, regulatory reporting.
**Deliverable:** compliance program management end-to-end.

## Phase 15 — Integrations
Slack, Teams, Google Drive, Dropbox, OneDrive, HRMS, payroll, CRM, calendars. REST API, webhooks,
integration marketplace.
**Deliverable:** documented public API + first-class integrations.

## Phase 16 — Mobile Experience
Offline mode, microlearning, push notifications, resume learning, certificates, downloads,
biometric login.
**Deliverable:** strong mobile/PWA (or native) experience.

## Phase 17 — AI Search
Semantic search across videos (timestamped), lessons, PDFs, concepts, questions, people, courses.
**Deliverable:** unified AI semantic search.

## Phase 18 — Enterprise Administration
Audit logs, themes, permissions, storage, approvals, feature flags, org settings, billing hooks,
support tools, impersonation.
**Deliverable:** complete admin control plane.

## Phase 19 — Security Hardening
Encryption, SOC 2 architecture, GDPR architecture, rate limiting, session management, IP
restrictions, backups, threat detection, secrets management.
**Deliverable:** audited, hardened, production-ready security posture.

---

## Final goal
A production-ready, AI-native, multi-tenant enterprise LMS/LXP with enterprise-grade architecture,
exceptional UI/UX/EX, AI integrated into every major workflow, extensible APIs/integrations, robust
security/compliance foundations, and production-quality docs, tests, and deployment readiness.
