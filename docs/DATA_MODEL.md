# Data Model

> Prisma + PostgreSQL. Multi-tenant by `organizationId` on every tenant-owned row.
> This document defines conventions and the core entities. The authoritative schema lives in
> `prisma/schema.prisma` (built in Phase 0+). Extend per phase; never break tenant scoping.

## Conventions

- **IDs:** `cuid()` (or `uuid`) primary keys named `id`.
- **Tenant column:** every tenant-owned model has `organizationId String` + relation +
  `@@index([organizationId])`. Composite indexes lead with `organizationId`.
- **Timestamps:** `createdAt DateTime @default(now())`, `updatedAt DateTime @updatedAt`.
- **Soft delete:** `deletedAt DateTime?` where audit/recovery matters; filter in data-access layer.
- **Enums** for fixed sets (roles, statuses). **JSONB** for flexible config (branding, settings).
- **Money/time:** store durations in seconds (int), money in minor units (int) + currency code.
- **Every foreign key indexed.** Every list query has a supporting index.

## Tenant isolation rule

The Prisma client is wrapped so that any query against a tenant-owned model **must** carry an
`organizationId` filter, injected from `TenantContext`. Queries without it throw. Only Super Admin
platform operations may bypass, through an explicit, audited escape hatch.

## Core entities (Phase 0–2)

```
Organization
  id, name, slug, domain, status
  branding(Json: logo, colors, emailTemplates)
  settings(Json: policies, security, locale)
  storageNamespace
  createdAt, updatedAt

User                       # global identity
  id, email(unique), name, avatarUrl, status
  emailVerifiedAt, mfaEnabled, mfaSecret(encrypted)
  createdAt, updatedAt

Membership                 # user ↔ organization (a user can belong to many orgs)
  id, organizationId, userId
  roles(Role[])           # RBAC roles within this org
  departmentId?, managerId?(User), title
  status(invited|active|suspended)
  @@unique([organizationId, userId])

Role (enum)               # SUPER_ADMIN, ORG_ADMIN, HR, LEARNING_MANAGER,
                          # INSTRUCTOR, REVIEWER, TEAM_LEAD, LEARNER, GUEST
Permission                # granular; mapped to roles via policy module (code, not just DB)

Department
  id, organizationId, name, parentId?
Team
  id, organizationId, name, leadId?(User)

AuthAccount               # OAuth/SAML/LDAP links (provider, providerAccountId, ...)
Session                   # server-validated sessions
SsoConnection             # per-org SAML/LDAP config (encrypted secrets)

Profile                   # extended learner profile (1:1 with Membership)
  id, membershipId
  skills(Json/relation), interests, designation
  certifications(relation), learningHistory(relation)

AuditLog                  # append-only
  id, organizationId?, actorUserId?, action, resourceType, resourceId
  metadata(Json), ip, userAgent, createdAt
  @@index([organizationId, createdAt])

Notification
  id, organizationId, userId, type, payload(Json), readAt, createdAt

FeatureFlag
  id, key, organizationId?(null = global), enabled, rolloutJson
```

## Domain entities by phase (high level)

- **Phase 3 — Courses:** `Course`, `Module`, `Lesson`, `ContentBlock` (rich text/media/code/
  callout/embed), `MediaAsset`, `ScormPackage`, `Transcript`, `Assignment`, `Submission`.
- **Phase 4 — AI authoring:** `GenerationJob`, `GeneratedOutline` (editable draft graph).
- **Phase 5 — Learning paths:** `LearningPath`, `PathNode`, `PathEdge` (conditional), `Enrollment`.
- **Phase 6 — Skills:** `Skill`, `Competency`, `ProficiencyLevel`, `RoleSkillMap`, `SkillAssessment`,
  `SkillGap`.
- **Phase 7 — Assessments:** `Assessment`, `QuestionBank`, `Question` (typed), `Attempt`, `Answer`,
  `Grade`.
- **Phase 8 — AI tutor:** `Conversation`, `Message`, `EmbeddingChunk` (pgvector), `RagSource`.
- **Phase 9 — Certs:** `Certificate`, `Badge`, `Verification` (QR/public), `Expiry`.
- **Phase 10 — Gamification:** `XpLedger`, `Coin`, `BadgeAward`, `Streak`, `Level`, `Challenge`.
- **Phase 11 — Social:** `Community`, `Thread`, `Post`, `Comment`, `Reaction`, `StudyGroup`.
- **Phase 12 — Live:** `LiveSession`, `Attendance`, `Poll`, `Recording`.
- **Phase 13 — Analytics:** mostly read models / materialized views over the above.
- **Phase 14 — Compliance:** `Policy`, `MandatoryAssignment`, `Escalation`, `ComplianceRecord`.
- **Phase 15 — Integrations:** `Integration`, `Webhook`, `ApiKey`.
- **Phase 18 — Admin:** ties together flags, themes, approvals, billing hooks, impersonation logs.

## Migrations & seed

- Every schema change ships a Prisma migration; migrations are reviewed and reversible where feasible.
- The **seed system** (`prisma/seed.ts`) creates: a demo org, roles/permissions, sample users per
  role, and (through Phase 2 only) sample content. Seeds are idempotent and environment-aware
  (never seed demo data into production).

## Indexing & performance

- Lead composite indexes with `organizationId`.
- Add indexes for sort/filter columns used by list views and analytics.
- Use `EXPLAIN` on heavy analytics queries; prefer materialized views for dashboards (Phase 13).
- pgvector: appropriate index (e.g. HNSW/IVFFlat) on embedding columns, tenant-filtered queries.
