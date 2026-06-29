# Security & Compliance

> Enterprise-first. Security and tenant isolation are designed in from Phase 0.
> This document is the security baseline; Phase 19 hardens and audits against it.

## 1. Tenant isolation (the most important control)

- Shared DB / shared schema with `organizationId` on every tenant-owned row.
- The data-access layer **forces** a tenant filter on every query via Prisma client extension;
  un-scoped queries on tenant models throw at runtime and fail tests.
- Storage keys namespaced per tenant; signed URLs only; no public buckets.
- Cross-tenant access only for Super Admin via explicit, audited admin paths.
- **Isolation tests are mandatory:** for each tenant-owned resource, a test proves tenant A cannot
  read/write tenant B's data through any route.

## 2. AuthN

- Auth.js/Better Auth; email+password (hashed with a modern KDF — argon2/bcrypt), Google,
  Microsoft, enterprise **SAML** and **LDAP**.
- **MFA (TOTP)**; per-tenant policy can require it. MFA secrets encrypted at rest.
- Server-validated sessions; secure, httpOnly, sameSite cookies; rotation on privilege change;
  idle + absolute timeouts; revocation on password reset / sign-out-all.

## 3. AuthZ (RBAC)

- Roles: Super Admin, Org Admin, HR, Learning Manager, Instructor, Reviewer, Team Lead, Learner,
  Guest. Roles map to granular permissions.
- Central policy module: `can(actor, action, resource)` — decisions in the service layer, never in
  the UI alone. UI hides what users can't do; the server still enforces it.
- Deny-by-default; least privilege; explicit checks on every mutation.

## 4. Input validation & output safety

- Zod validation at every boundary (route handlers, server actions, webhooks, job payloads).
- Output encoding to prevent XSS; sanitize rich text/HTML; safe handling of SCORM/xAPI content.
- Parameterized queries only (Prisma); no string-built SQL.
- File uploads: type/size limits, content-type checks, AV scanning hook, render in sandbox.

## 5. Secrets & data protection

- Secrets only via validated env (`src/env.ts`); never committed, never logged.
- Encryption at rest for sensitive fields (MFA secrets, SSO/LDAP credentials, integration tokens)
  using `ENCRYPTION_KEY`; TLS in transit everywhere.
- PII minimization; PII never sent to AI providers without a policy check + redaction.

## 6. Platform hardening

- **Rate limiting** (Redis) per IP + per user + per tenant on sensitive endpoints.
- Security headers (CSP, HSTS, X-Frame-Options, etc.); CSRF protection on state-changing routes.
- Session management UI (active sessions, revoke); optional **IP restrictions** per tenant.
- Webhook signature verification; API keys scoped + rotatable; least-privilege service creds.
- Dependency scanning, secret scanning, and SAST in CI.

## 7. Auditability

- Append-only `AuditLog` for every meaningful mutation: who, what, which resource, when, from where.
- Admin-visible audit trails; tamper-evident (no edits/deletes through the app).
- Impersonation (Phase 18) is fully logged and time-boxed.

## 8. Compliance posture (architecture-level)

- **SOC 2-ready** controls: access control, change management, logging, monitoring, backups.
- **GDPR-ready:** data export, right-to-erasure workflows, data residency awareness, consent records.
- Backups + tested restore; threat detection hooks; incident runbook (Phase 19).

## 9. Security Definition of Done (per phase)

- [ ] New resources are tenant-scoped and have isolation tests.
- [ ] New mutations are authz-checked via the policy module and audit-logged.
- [ ] New inputs are Zod-validated; new outputs are safely encoded.
- [ ] No secrets in code/logs; sensitive new fields encrypted.
- [ ] Rate limiting / abuse considerations addressed for new public endpoints.
