# Feature: NNN — <Short feature name>

| Field | Value |
|-------|-------|
| **ID** | NNN |
| **Status** | `draft` |
| **Owner** | _unassigned_ |
| **Created** | YYYY-MM-DD |
| **Last updated** | YYYY-MM-DD |
| **Depends on** | _feature ids, or `none`_ |
| **Blocks** | _feature ids, or `none`_ |
| **ADRs** | _ADR ids relevant to this feature_ |

**Status values**

| Status | Meaning | Who sets it |
|--------|---------|--------------|
| `draft` | Being specified; acceptance criteria may still change | author |
| `ready` | Fully specified and approved; safe to implement | human reviewer |
| `implementing` | Work in progress | implementer |
| `review` | Implemented and tested; awaiting human review | implementer |
| `completed` | Reviewed and accepted | human reviewer |
| `blocked` | Cannot proceed; an open question or missing dependency is named below | anyone |

---

## 1. User Story

> As a _role_, I want _capability_, so that _benefit_.

**Traceability**

| Requirement | Source |
|-------------|--------|
| _user story id_ | `01-product/PRD.md` § _User Stories_ |

---

## 2. Acceptance Criteria

Each criterion must be independently verifiable. Checked boxes mean the criterion was verified, with the evidence recorded in section 8.

### Functional

- [ ] **AC-1** Given _state_, when _action_, then _observable result_.
- [ ] **AC-2** Given _invalid input_, when _action_, then _specific error and status code_.

### Error and edge cases

- [ ] **AC-3** Given _boundary value_, when _action_, then _defined behavior_ (not "handle gracefully").
- [ ] **AC-4** Given _the dependency is unavailable_, when _action_, then _documented failure mode_.

### Non-functional

- [ ] **AC-5** _Endpoint_ responds within _X ms_ at p95 under _Y_ concurrent requests.
- [ ] **AC-6** _The action_ is recorded in the audit log with _fields_.

---

## 3. Technical Approach

**Design references**

| Concern | Document | Section |
|---------|----------|---------|
| Architecture | `02-design/ARCHITECTURE.md` | |
| Domain | `02-design/DOMAIN.md` | |
| Data | `02-design/DATA_MODEL.md` | |
| API | `02-design/API.md` | |

**Approach**

_How the feature will be built, in terms the design documents already use. Name the layers involved and the flow of control._

**Files to create**

- `path/to/file.ext` — purpose

**Files to change**

- `path/to/file.ext` — what changes and why

**Data model changes**

- New table/column/index, or `none`

**API changes**

- New/changed endpoint, or `none`

**Explicitly out of scope for this feature**

- _Thing a reader might reasonably expect but that belongs elsewhere_

---

## 4. Dependencies

| Type | Name | Required for | Available? |
|------|------|--------------|------------|
| Feature | _NNN_ | | yes/no |
| External service | | | |
| ADR | | | |
| Human decision | | | |

---

## 5. Test Plan

| Level | Test file | What it proves | Criterion |
|-------|-----------|----------------|-----------|
| Unit | | | AC-1 |
| Integration | | | AC-2 |
| E2E | | | |

**Commands to run**

```
<lint>
<typecheck>
<build>
<test>
```

**Test data**

_Factories, fixtures, or seed data required._

---

## 6. Rollback Plan

| Concern | Answer |
|---------|--------|
| How is this reverted in code? | |
| Is a data migration involved? | yes/no — if yes, how is it reversed? |
| How is a failure detected after release? | _metric, alert, or log signal_ |
| What is the rollback trigger? | _specific condition_ |
| Who can roll back? | |

---

## 7. Security Review

- [ ] Every new endpoint/state change has an explicit authorization decision: _describe it_
- [ ] New inputs are validated and bounded: _describe it_
- [ ] No new secret or credential is introduced
- [ ] New data is classified in `DATA_MODEL.md`
- [ ] New log fields reviewed for PII
- [ ] Abuse cases considered: _list them_

---

## 8. Implementation Log

_Date · what changed · what was verified · what was not verified._

| Date | Change | Verified by | Unverified / assumed |
|------|--------|-------------|----------------------|
| | | | |

---

## 9. Open Questions

| # | Question | Blocks | Who answers | Status |
|---|----------|--------|-------------|--------|
| Q1 | | | | open |

A feature with an open blocking question stays `blocked` until it is resolved. The question is written down; it is not guessed at.
