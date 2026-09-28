# Security Guidelines

> Status: `draft`
> Applies to: all code in this repository
> Last updated: _YYYY-MM-DD_

Security requirements are **release-blocking**, not advisory. A feature that violates this document cannot be marked `completed`.

---

## 1. Authentication & Authorization

### Authentication

- Passwords: minimum _12_ characters, maximum _128_ (to bound hashing cost), checked against a breached-password list, hashed with Argon2id (or bcrypt with cost ≥ 12) using a per-user salt.
- Never log, log-request bodies for, or return passwords, tokens, or session identifiers — including in error messages.
- Sessions: short-lived access tokens, rotating refresh tokens, server-side revocation list, and an absolute session lifetime.
- MFA (TOTP or WebAuthn) is required for accounts with _admin_ or _billing_ privileges.
- Password reset and email-change tokens are single-use, time-limited, and stored hashed.

### Authorization

- **Deny by default.** Every request is authorized explicitly; the absence of a check is a bug.
- **Object-level checks, not just route-level.** Verify the caller may access *this specific* resource (IDOR is the most common serious finding).
- Roles: _e.g. `owner`, `admin`, `member`, `viewer`_ — assign the minimum required role.
- Enforce authorization in the domain/service layer so it cannot be bypassed by a new endpoint.
- Multi-tenant data is isolated by tenant id **in the query itself**, never by filtering in application code after the fetch.

| Resource | Action | Required role/permission |
|----------|--------|--------------------------|
| _Resource_ | _action_ | _permission_ |

---

## 2. Data Protection

- **Classification:** every table/column is classified in `02-design/DATA_MODEL.md` section 6. Unclassified data is treated as confidential.
- **Encryption in transit:** TLS 1.2+ everywhere, including internal service-to-service calls and database connections. HSTS enabled.
- **Encryption at rest:** provider-managed encryption by default; application-level envelope encryption for fields classified as restricted.
- **PII in logs:** prohibited. Mask or hash identifiers in log output.
- **PII in analytics and third parties:** only through an explicit allowlist in this document, with a signed data processing agreement.
- **Data retention:** each data class has a retention period; deletion jobs are automated and tested.
- **Right to erasure:** a documented, tested path to delete a user's personal data across primary store, caches, search indexes, and downstream processors.
- **Backups:** encrypted, access-controlled, and tested for restore at least _quarterly_.

---

## 3. Input Validation

- Validate at the boundary. Never trust input that crossed a process, queue, or network boundary.
- Use **allow-lists** over deny-lists for fields, content types, and accepted values.
- **Parameterized queries only.** No string-concatenated SQL, ever. No ORM escape hatches (`raw`, `$queryRawUnsafe`) without a written justification and review.
- Output is encoded for its sink: HTML templating with auto-escaping, context-aware encoding for any raw output.
- File uploads: validate content by magic bytes (not the client-declared MIME type), cap size, store outside the web root, and serve with `Content-Disposition: attachment` and a fixed `Content-Type`.
- URLs from users: parse and allow-list schemes (`https`), reject `javascript:` and `data:`.
- Numeric and length limits on every field. Unbounded input is a denial-of-service vector.
- Deserialization: use a safe format, disable polymorphic type resolution, and never deserialize untrusted data into executable types.

---

## 4. OWASP Top 10 Mitigations

| Risk | Mitigation in this system | Verified by |
|------|---------------------------|-------------|
| Broken access control | Deny-by-default, object-level checks, tenant scoping in the query | Integration tests per protected route |
| Cryptographic failures | TLS 1.2+, Argon2id/bcrypt, secrets manager, no hardcoded secrets | Security test + dependency scan |
| Injection | Parameterized queries, allow-listed validation, context-aware output encoding | Static analysis + injection tests |
| Insecure design | Feature spec must name abuse cases; threat model in feature review | Feature review checklist |
| Security misconfiguration | Hardened defaults, no debug mode in production, headers set centrally | Deployment checklist |
| Vulnerable components | Lockfile committed, automated dependency and image scanning, patch SLA | CI scan gate |
| Identification & authentication failures | Rate limits, lockout policy, MFA for privileged accounts, session rotation | Auth integration tests |
| Software/data integrity failures | Signed CI artifacts, reviewed migrations, integrity checks on webhooks | CI verification |
| Logging & monitoring failures | Structured audit log, alerting on auth failures and privilege changes | Alert tests |
| SSRF | Egress allow-list, no user-controlled URLs in fetch calls, block internal ranges | SSRF test suite |

---

## 5. Secrets Management

- **Never** commit secrets. `.env*` files are git-ignored except `.env.example`, which must contain placeholders only.
- Secrets live in the platform's secret manager (e.g. _Vault / AWS Secrets Manager / InsForge secrets_) and are injected at runtime.
- No secrets in source, tests, fixtures, snapshots, CI logs, error messages, or client bundles.
- Every credential is scoped to the minimum privilege, has a named owner, and has a documented rotation procedure.
- **If a secret is ever committed:** treat it as compromised. Rotate it first, then remove it from history, then record the incident. Removing the file alone is not a fix.
- `.env.example` documents every variable name, its purpose, whether it is required, and a safe placeholder.

| Secret | Where it lives | Owner | Rotation procedure |
|--------|----------------|-------|--------------------|
| _DATABASE_URL_ | _secret manager_ | _team_ | _steps_ |

---

## 6. Audit Logging

Log **security-relevant actions** in a structured, append-only log: authentication and failed attempts, authorization denials, privilege changes, data export, data deletion, and configuration changes.

Each entry contains: timestamp (UTC), actor id, action, target resource and id, outcome, source IP, `requestId`, and user agent.

Rules:

- Never log credentials, tokens, full request bodies for authenticated routes, or raw PII.
- Audit logs are retained for _N_ days (regulatory minimum), stored separately from application logs, and access-restricted.
- Audit records are not modifiable by application code.
- Alerting is defined for _N_ events, such as repeated auth failure from one source or any privilege change.

---

## 7. Security Review Checklist

Run before marking a feature `completed`:

- [ ] Every new endpoint has an explicit authorization decision recorded.
- [ ] New inputs are validated and bounded; new queries are parameterized.
- [ ] No new secret, token, or credential is present in code or config.
- [ ] New data is classified in `DATA_MODEL.md`.
- [ ] New log fields are reviewed for PII.
- [ ] New dependencies are necessary, pinned, and pass scanning.
- [ ] Security-relevant behavior has a test.
- [ ] Threat-model changes for this feature are noted in the feature file.
