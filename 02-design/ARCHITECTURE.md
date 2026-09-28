# System Architecture

> Status: `draft`
> Related: `01-product/PRD.md`, `DOMAIN.md`, `DATA_MODEL.md`, `API.md`
> Last updated: _YYYY-MM-DD_

This document defines **how** the system is built. It must not introduce capabilities that the PRD does not describe.

---

## 1. Overview

_A short description of the system, its boundaries, and the responsibilities it owns. Three to five paragraphs maximum._

**System boundary**

_In what the system is responsible, and what it delegates to other systems._

**External systems**

| System | Direction | Purpose | Contract |
|--------|-----------|---------|----------|
| _Stripe_ | outbound | _Payments_ | _link to API doc / spec_ |

---

## 2. Technology Stack

| Layer | Choice | Version | Rationale (traceable to) |
|-------|--------|---------|--------------------------|
| Language | | | |
| Framework | | | |
| Database | | | |
| ORM / query layer | | | |
| Auth | | | |
| Hosting | | | |
| CI/CD | | | |

> Adding or replacing a dependency requires an ADR in `04-ai/DECISIONS.md`.

---

## 3. System Components

One subsection per component. Every component must be justified by a requirement in the PRD.

### Component name

- **Responsibility:**
- **Interfaces provided:** (APIs, events, jobs)
- **Interfaces consumed:**
- **Data owned:**
- **Failure mode:** what happens when it is unavailable
- **Scaling characteristics:**

_(Repeat per component.)_

---

## 4. Data Flow

Describe the primary flows end to end.

### Flow 1 — _name_

1. _Client/system initiates ..._
2. _..._
3. _Persisted state changes: ..._
4. _Response: ..._

### Asynchronous flow

_Event or job triggered by ..., consumed by ..., retried ..., dead-lettered ..._

---

## 5. Deployment Architecture

- **Environments:** _dev / staging / production — how they differ_
- **Deployment method:** _CI pipeline, IaC tool_
- **Topology:** _which components run where; network boundaries_
- **Secrets delivery:**
- **Database migration during deploy:** _expand/migrate/contract, backward compatibility window_
- **Rollback procedure:**

```
<commands or steps>
```

---

## 6. Non-functional Requirements

| Concern | Requirement | How it is verified |
|---------|-------------|--------------------|
| Availability | | |
| Performance (p95 latency) | | |
| Throughput | | |
| Scalability ceiling | | |
| Data durability / RPO / RTO | | |
| Observability | | |
| Accessibility | | |
| Compliance | | |

Every row must name a verification method. Unverifiable requirements are aspirations, not requirements.

---

## 7. Architecture Diagram

```
<ASCII or mermaid diagram>
```

---

## 8. Decisions

Architectural decisions are recorded in `04-ai/DECISIONS.md`, not inlined here. This section links to them.

| ADR | Title | Status |
|-----|-------|--------|
| ADR-001 | | |
