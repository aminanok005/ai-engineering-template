# Data Model

> Status: `draft`
> Related: `DOMAIN.md`, `API.md`
> Last updated: _YYYY-MM-DD_

The data model is the persistence projection of the domain. If it conflicts with `DOMAIN.md`, the domain wins and this document is corrected.

---

## 1. Entity Relationship Diagram

```
<ASCII or mermaid ERD>
```

---

## 2. Table Definitions

One subsection per table. Every column is documented; `null` semantics are explicit.

### Table: `users`

| Column | Type | Null | Default | Constraints | Description |
|--------|------|------|---------|-------------|-------------|
| `id` | `uuid` | no | `gen_random_uuid()` | PK | |
| `email` | `citext` | no | | UNIQUE, NOT NULL | Normalized to lowercase |
| `created_at` | `timestamptz` | no | `now()` | | |

**Indexes:** see section 3.
**Retention:** _how long rows are kept and how they are deleted._
**Expected size at 12 months:** _order of magnitude._

_(Repeat per table.)_

---

## 3. Indexes

| Table | Index | Definition | Serves query | Required by |
|-------|-------|------------|--------------|-------------|
| `users` | `users_email_idx` | `(email)` | _lookup by email_ | _feature 001_ |

Every index must name the access pattern it serves. Unused indexes are write-amplification with no benefit.

---

## 4. Migrations Strategy

- **Tool:** _name_
- **Directory convention:** _e.g. `migrations/NNNN_description.sql`_
- **Rules:**
  - Migrations are append-only. Never edit an applied migration.
  - Every migration must be reversible, or must document why it is not.
  - Use the expand/migrate/contract pattern for destructive changes:
    1. **Expand** — add the new nullable column/table alongside the old one.
    2. **Migrate** — backfill, and dual-write while both exist.
    3. **Contract** — remove the old structure only after the application no longer reads it.
  - Schema changes deploy separately from and before the code that requires them.
- **Zero-downtime rule:** never combine a destructive change with the code deploy that needs it.

---

## 5. Data Access Patterns

The queries the system must perform efficiently. These drive the schema.

| # | Operation | Called by | Expected shape | Latency budget | Index used |
|---|-----------|-----------|----------------|-----------------|-------------|
| 1 | _find user by email_ | _auth_ | _point lookup_ | _< 20 ms p95_ | _`users_email_idx`_ |

If an access pattern is not listed here, it is not a supported query. Ad-hoc queries require a migration review.

---

## 6. Data Classification

Aligned with `03-engineering/SECURITY.md`.

| Table/Column | Classification | Contains PII | Encryption at rest | Logged |
|--------------|----------------|---------------|----------------------|--------|
| `users.email` | _Confidential_ | yes | provider default | never |

---

## 7. Seeding and Local Development

- Initial data: _file/location_
- Fixtures: _location and generator_
- Real user data must never be copied into local or test environments.
