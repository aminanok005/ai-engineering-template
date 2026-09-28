# API Contract

> Status: `draft`
> Related: `01-product/PRD.md`, `DATA_MODEL.md`, `03-engineering/SECURITY.md`
> Last updated: _YYYY-MM-DD_

The API contract is the boundary between this system and its consumers. It is the integration test surface.

---

## 1. Conventions

- **Style:** REST over HTTPS (JSON). GraphQL is used for _scope_ if applicable.
- **Base URL:** `https://api.example.com/v1`
- **Content type:** `application/json`
- **Identifiers:** opaque UUIDs, never sequential integers in public responses.
- **Timestamps:** ISO 8601, UTC, `Z` suffix.
- **Pagination:** cursor-based (`?cursor=<opaque>&limit=<n>`). Offset pagination is not allowed on mutable collections.
- **Field naming:** `camelCase` in JSON, `snake_case` in the database.
- **Money:** integer minor units plus ISO currency code. Never floating point.

---

## 2. Endpoints

| Method | Path | Purpose | Auth | Rate limit | Feature |
|--------|------|---------|------|-------------|---------|
| `POST` | `/v1/auth/register` | Create an account | none | _10/min/IP_ | 001 |
| `GET` | `/v1/users/me` | Current user profile | bearer | _60/min/user_ | 003 |

### `POST /v1/auth/register`

**Purpose:** _one line_
**Authorization:** _role required_
**Idempotency:** _supported via `Idempotency-Key`, or not supported_

**Request**

```json
{
  "email": "user@example.com",
  "password": "..."
}
```

| Field | Type | Required | Rules |
|-------|------|----------|-------|
| `email` | string | yes | valid email, max 254 chars, normalized lowercase |
| `password` | string | yes | min 12 chars, checked against a breached-password list |

**Success responses**

| Status | Body | When |
|--------|------|------|
| `201` | _user resource_ | created |
| `409` | _error_ | email already registered |

**Error responses**

| Status | `code` | Condition |
|--------|-------|-----------|
| `400` | `validation_error` | field validation failed |
| `422` | `unprocessable` | syntactically valid but semantically rejected |
| `429` | `rate_limited` | limit exceeded |

_(Repeat per endpoint.)_

---

## 3. Request/Response Schemas

Shared schemas are defined once and referenced by every endpoint.

| Schema | Definition | Used by |
|--------|------------|---------|
| `User` | `{ id, email, createdAt }` — never includes password hashes, tokens, or internal flags | |
| `Error` | `{ error: { code, message, details?, requestId } }` | all endpoints |

Schema source of truth: _OpenAPI file / JSON Schema / zod definitions — location._

---

## 4. Authentication

- **Mechanism:** _e.g. OAuth 2.1 Authorization Code + PKCE; short-lived access token, rotating refresh token_
- **Token storage (client):**
- **Token lifetime:** access _X min_, refresh _X days_
- **Transport:** `Authorization: Bearer <token>`; refresh token in `HttpOnly`, `Secure`, `SameSite=Strict` cookie.
- **Revocation:** _mechanism and propagation delay_

---

## 5. Rate Limiting

| Scope | Limit | Key | Response on breach | Exemptions |
|-------|-------|-----|--------------------|------------|
| Public endpoints | _10 / min_ | IP | `429` + `Retry-After` | |
| Authenticated | _60 / min_ | user or API key | `429` | |

- Implementation: _token bucket / sliding window; store_
- Headers returned: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `Retry-After`
- Authenticated limit failures are audit-logged.

---

## 6. Error Handling

One consistent error envelope for every failure.

```json
{
  "error": {
    "code": "validation_error",
    "message": "Human-readable, safe to show a user",
    "details": [{ "field": "email", "issue": "invalid_format" }],
    "requestId": "uuid"
  }
}
```

Rules:

- `message` never contains stack traces, SQL, file paths, or secrets.
- Unexpected errors return a generic message and a `requestId`; the detail lives in server logs.
- `details` is only present for validation failures.
- Every response carries `X-Request-Id` (accept an inbound one or generate it).

| Status | Meaning | Client action |
|--------|---------|---------------|
| `400` | malformed request | fix and retry |
| `401` | missing/invalid authentication | re-authenticate |
| `403` | authenticated but not permitted | do not retry |
| `404` | resource does not exist or is not visible | do not retry |
| `409` | conflict with current state | refresh and retry |
| `422` | semantically invalid | fix and retry |
| `429` | rate limited | retry after `Retry-After` |
| `500` | unexpected server error | retry with backoff, report `requestId` |

---

## 7. Versioning Strategy

- **Scheme:** major version in the path (`/v1`). Breaking changes only.
- **Additive changes** (new optional field, new endpoint) ship without a version bump and must be backwards compatible for existing consumers.
- **Deprecation:** announce at least _90 days_ before removal, mark with `Deprecation` and `Sunset` headers, and document the replacement.
- **Support policy:** the current major and the previous major are supported.
- **Migration guide:** every breaking version ships with a documented migration path in this file.

---

## 8. Webhooks and Events

| Event | Endpoint/subscription | Payload | Signature | Retry policy |
|-------|----------------------|---------|-----------|---------------|
| _event_ | | | _HMAC-SHA256_ | _exponential backoff, 24 h window_ |

Webhook consumers must verify the signature and reject stale timestamps to prevent replay.
