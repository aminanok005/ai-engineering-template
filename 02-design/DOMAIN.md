# Domain Model

> Status: `draft`
> Related: `01-product/PRD.md`, `DATA_MODEL.md`
> Last updated: _YYYY-MM-DD_

The domain model captures the **business language and rules**. Persistence details belong in `DATA_MODEL.md`.

---

## 1. Ubiquitous Language

Terms used consistently in code, tests, and documentation.

| Term | Definition | Not to be confused with |
|------|------------|-------------------------|
| _Term_ | _Definition_ | _Similar term_ |

---

## 2. Bounded Contexts

| Context | Responsibility | Owns | Upstream | Downstream |
|---------|----------------|------|----------|------------|
| _Identity_ | _Authentication and accounts_ | _User_ | — | _Billing_ |

### Context map

```
<ASCII or mermaid diagram showing context relationships and translation boundaries>
```

**Anti-corruption layers:** _Where one context's model is translated into another's._

---

## 3. Aggregates

An aggregate is a consistency boundary. Transactions never span aggregates.

### Aggregate: _Name_

- **Root entity:**
- **Invariants it protects:**
- **Boundary (what is inside vs outside):**
- **Lifecycle:** _states and transitions_
- **Concurrent access strategy:** _optimistic locking / serializable / none_

_(Repeat per aggregate.)_

---

## 4. Entities

Entities have identity and a lifecycle.

| Entity | Aggregate | Identity | Key attributes | States |
|--------|-----------|----------|----------------|--------|
| _Entity_ | _Aggregate_ | _uuid / natural key_ | | |

---

## 5. Value Objects

Immutable, compared by value, no identity.

| Value Object | Fields | Validation rules | Used by |
|--------------|--------|------------------|----------|
| _Email_ | _local, domain_ | _must be normalized lowercase_ | _User_ |

---

## 6. Domain Services

Logic that does not belong to a single entity or value object.

| Service | Operation | Rule enforced | Reason it is not a method |
|---------|-----------|---------------|----------------------------- |
| _PricingService_ | _quote(order)_ | _pricing depends on tier and date_ | _spans Order and Customer_ |

---

## 7. Domain Events

| Event | Raised by | Meaning | Consumers |
|-------|-----------|---------|-----------|
| _UserRegistered_ | _Identity_ | _A user completed registration_ | _Email, Analytics_ |

Event delivery guarantees: _at-least-once / exactly-once; ordering assumptions._

---

## 8. Invariants

Business rules that must hold at all times. Each is testable.

| # | Invariant | Enforced by | Test |
|---|-----------|-------------|------|
| I1 | _An order with a discount must have a coupon_ | _Order_ | _`order.spec.ts`_ |

---

## 9. Glossary Changes Log

New terms introduced during implementation are added here in the same commit that introduces them in code.
