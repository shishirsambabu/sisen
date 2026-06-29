# Phase 1 — Authentication & Organizations

> Goal: organizations can fully onboard, with enterprise-grade auth and complete tenant isolation.
> Builds directly on the Phase 0 multi-tenancy + RBAC foundation.

## Scope

### Authentication
- **Email + password** (modern KDF: argon2/bcrypt), email verification, password reset.
- **Google** and **Microsoft** OAuth.
- **MFA (TOTP)** enrollment + challenge; per-tenant policy to require MFA; encrypted secrets;
  recovery codes.
- **SAML** SSO (per-org `SsoConnection`, SP metadata, ACS handling).
- **LDAP** bind/authentication + directory lookup (per-org config, encrypted credentials).
- Secure sessions: httpOnly/secure/sameSite cookies, rotation on privilege change, idle + absolute
  timeouts, sign-out-all / revocation.

### Roles & permissions
- Full role set live: Super Admin, Org Admin, HR, Learning Manager, Instructor, Reviewer, Team Lead,
  Learner, Guest. Mapped to the permission catalog through the `can()` policy module.
- Role assignment UI for Org Admins (within their tenant only).

### Organizations (multi-tenant)
- Org creation/onboarding wizard (immersive, progressive disclosure per `DESIGN_SYSTEM.md`).
- **Branding:** logo, primary color, theme → applied via the theme engine.
- **Domain** mapping (and domain-based SSO routing).
- **Email templates** per org (invite, verification, reset, notifications).
- **User directory:** invite, bulk import, deactivate, search, filter; department/team/manager.
- **Policies:** password policy, MFA requirement, session/IP rules surfaced here.
- **Tenant + storage isolation** enforced and **tested** (cross-tenant denial).

## AI opportunities
- Smart onboarding assistant (suggests org structure/departments from a short description).
- Anomaly hints on the auth/admin surface (e.g. unusual sign-in patterns) — log-driven, optional.

## Key entities (extend from `DATA_MODEL.md`)
`Organization`, `Membership`, `AuthAccount`, `Session`, `SsoConnection`, `Department`, `Team`,
plus invite tokens and password-reset tokens.

## UI surfaces
Sign-in / sign-up / SSO callback; MFA enroll/challenge; org onboarding wizard; org settings
(branding, domain, email templates, policies); user directory + invites; role management.
All with loading/empty/error/success states, keyboard support, AA accessibility.

## Tests (must pass)
- Each auth method signs a user in and creates a correctly-scoped session.
- MFA required-policy blocks sign-in until satisfied; recovery codes work.
- SAML/LDAP flows (mocked IdP/directory in integration tests) map to the right org + roles.
- **Isolation:** Org Admin of tenant A cannot view/modify tenant B users, settings, or storage.
- AuthZ: each role's allowed/denied admin actions enforced server-side.
- Invites + onboarding wizard create valid memberships; branding/theme applies per tenant.

## Definition of Done
Shared DoD + "an organization can be created, branded, configured, and have users onboarded via any
supported auth method, with tenant isolation proven by tests."
