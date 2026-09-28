# Product Requirements Document

> Status: `draft`
> Owner: _unassigned_
> Last updated: _YYYY-MM-DD_

This document defines **what** we are building and **why**. It does not define how. Technical design belongs in `02-design/`.

---

## 1. Problem Statement

Describe the problem in the user's own terms.

- **Who has this problem?**
- **What do they do today instead?**
- **Why is that insufficient?**
- **What is the cost of leaving it unsolved?**

> Replace this block. Do not leave placeholder text in a document that a design phase depends on.

---

## 2. Target Users

| Role | Description | Primary need | Technical skill level |
|------|-------------|--------------|-----------------------|
| _Role A_ | _Who they are_ | _What they need_ | _level_ |
| _Role B_ | | | |

### Non-users

_Who this product explicitly does not serve._

---

## 3. User Stories

Each story is written from the user's perspective and must be independently valuable.

### US-001 — _Story title_

> As a _role_, I want _capability_, so that _benefit_.

**Acceptance criteria**

- [ ] _Given ... when ... then ..._
- [ ] _Given ... when ... then ..._

**Priority:** must-have / should-have / could-have

_(Repeat per story.)_

---

## 4. Acceptance Criteria

Global criteria that apply to the release as a whole.

- [ ] _Criterion_
- [ ] _Criterion_

Per-feature criteria live in the feature files under `features/`. This section only records release-level expectations.

---

## 5. Success Metrics

| Metric | Definition | Baseline | Target | Measurement source |
|--------|------------|----------|--------|--------------------|
| _Metric_ | _How it is computed_ | _value_ | _value_ | _instrument/table_ |

Metrics must be measurable from the product itself. A metric with no data source is not a metric.

---

## 6. Out of Scope

Explicitly **not** being built now. This list prevents scope creep and gives the agent a hard boundary.

- _Excluded capability_
- _Excluded platform/integration_
- _Excluded performance/scale tier_

---

## 7. Constraints and Assumptions

### Constraints

- _Legal, budget, timing, platform, or compliance constraints._

### Assumptions

- _Assumption that, if wrong, would change the design._ Each assumption needs an owner who will confirm it.

---

## 8. Open Questions

| # | Question | Blocks | Who answers | Status |
|---|----------|--------|-------------|--------|
| Q1 | _Question_ | _Feature ids_ | _Name_ | open |

An unanswered question here is a legitimate reason to set a feature to `blocked`.

---

## 9. Revision History

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial draft | _name_ |
