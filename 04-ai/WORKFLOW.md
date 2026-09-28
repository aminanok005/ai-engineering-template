# AI Workflow

> Status: `draft`
> Binding rules live in `../AGENTS.md`. This document describes the process those rules operate in.
> Last updated: _YYYY-MM-DD_

---

## Overview

```
Product Requirements (01-product/PRD.md)
        ↓
[ Human review gate ]
        ↓
Engineering Blueprint (02-design, 03-engineering)
        ↓
[ Human review gate ]
        ↓
Feature Specifications (features/*.md)
        ↓
[ Human review gate ]
        ↓
Feature Implementation (one feature at a time)
        ↓
Tests + Validation
        ↓
[ Human review gate ]
        ↓
Documentation and decisions updated
```

Each phase produces documents, not code — until the implementation phase. Each phase ends with a stop-for-review point. The agent does not proceed past a gate on its own initiative.

---

## 1. Bootstrap Phase

**Prompt:** `prompts/01-bootstrap.md`
**Goal:** Establish the documentation foundation from the PRD.
**Produces:** A filled-in repository skeleton — no application code.

**Steps**

1. Read `AGENTS.md` and `01-product/PRD.md` in full.
2. Inspect the existing repository to understand what already exists.
3. Identify gaps between the PRD and the documentation that exists.
4. Create the directory structure and stub documents.
5. Record open questions in the PRD and flag anything ambiguous.

**Rules**

- No application code, scaffolding, or runtime configuration.
- No architecture decisions. Bootstrap records findings; design decides.
- Every unclear requirement becomes an open question, never an assumption.

**Gate:** human confirms the PRD is complete enough to design against. **Stop.**

---

## 2. Design Phase

**Prompt:** `prompts/02-design.md`
**Goal:** Produce the engineering blueprint.
**Produces:** `02-design/ARCHITECTURE.md`, `DOMAIN.md`, `DATA_MODEL.md`, `API.md`, `03-engineering/SECURITY.md`, `TESTING.md`, `CONVENTIONS.md`.

**Steps**

1. Re-read the PRD and the bootstrap output.
2. Define the system boundary, components, and data flow.
3. Model the domain: contexts, aggregates, entities, value objects, events, invariants.
4. Project the domain into a data model, including indexes and a migration strategy.
5. Define the API contract: endpoints, schemas, auth, errors, versioning.
6. Define security, testing, and coding rules for the chosen stack.
7. Record every significant choice as an ADR in `DECISIONS.md`.

**Rules**

- No application code.
- Every component traces to a PRD requirement. Anything else is scope creep.
- Anything that changes a decision already recorded in `DECISIONS.md` requires a superseding ADR, not a silent edit.
- Unresolved design questions are written down, not guessed.

**Gate:** human reviews the blueprint for correctness and completeness. **Stop.**

---

## 3. Feature Breakdown Phase

**Prompt:** `prompts/03-feature-breakdown.md`
**Goal:** Decompose the PRD into independently implementable features.
**Produces:** `features/NNN-<name>.md` using `features/FEATURE_TEMPLATE.md`.

**Steps**

1. Re-read the PRD and the full design set.
2. Identify independently valuable user journeys.
3. Order them so each feature depends only on features that precede it.
4. Write each feature file: user story, acceptance criteria, technical approach, dependencies, test plan, rollback plan.
5. Mark cross-cutting work (auth, observability, CI) as its own early features.
6. Flag any feature that cannot be implemented because a design question is unanswered.

**Rules**

- No application code.
- Each feature must be testable on its own and independently releasable, or must declare its dependency explicitly.
- A feature must be small enough to review as a single diff. If it is not, split it.
- Acceptance criteria are written as Given/When/Then and are checkable.

**Gate:** human approves the backlog and the ordering. **Stop.**

---

## 4. Implementation Phase

**Prompt:** `prompts/04-implement-feature.md`
**Goal:** Implement exactly one feature.
**Applies to:** one `features/NNN-<name>.md` per session.

**Steps**

1. **Understand** — read the feature file, the referenced design sections, and the existing code it touches.
2. **Plan** — restate the approach, list the files to change, and name the tests to add. Surface conflicts before writing code.
3. **Implement** — write the minimum code that satisfies the acceptance criteria, following `CONVENTIONS.md`.
4. **Test** — add unit tests for new behavior and integration tests for new boundaries, per `TESTING.md`.
5. **Validate** — run lint, typecheck, build, and the test suite. Report actual output.
6. **Review** — self-review against the definition of done in `AGENTS.md`; update the feature file's status.
7. **Update docs** — update the affected design documents and file the ADR if a decision was made.

**Rules**

- One feature per change set. No unrelated refactors.
- No test is weakened, deleted, or skipped to get a green build.
- Unverifiable claims are labeled as unverified, never as done.
- If the feature is blocked, set status to `blocked` and write the specific question and who must answer it.

**Gate:** human reviews the change against the feature file. Only then does the status become `completed`.

---

## 5. Review Gates

| Gate | After | Reviewer checks | Agent may proceed |
|------|-------|-----------------|------------------|
| G1 | Bootstrap | PRD is complete; open questions listed | No — human approval required |
| G2 | Design | Blueprint is correct, complete, consistent with the PRD | No — human approval required |
| G3 | Feature breakdown | Features are correctly scoped, ordered, and testable | No — human approval required |
| G4 | Feature implementation | Acceptance criteria met; tests pass; docs updated | No — human review required for `completed` |

Rules:

- A gate is not passed by the agent's own assessment.
- If a human rejects a gate, the agent revises the documents and returns to the same gate. It does not move forward.
- Every rejection and its resolution is recorded in the relevant document.

---

## 6. Documentation Updates

Documentation is part of the deliverable, not a follow-up task.

| Change | Documents to update |
|---------|---------------------|
| New or changed product requirement | `01-product/PRD.md` |
| New component, flow, or non-functional requirement | `02-design/ARCHITECTURE.md` |
| New or changed domain concept | `02-design/DOMAIN.md` |
| New or changed table, index, or access pattern | `02-design/DATA_MODEL.md` |
| New or changed endpoint or schema | `02-design/API.md` |
| New security surface | `03-engineering/SECURITY.md` |
| New testing pattern | `03-engineering/TESTING.md` |
| New coding pattern | `03-engineering/CONVENTIONS.md` |
| Architectural choice | `04-ai/DECISIONS.md` (ADR) |
| Feature progress, verification, or open questions | the feature's own file |

Rules:

- Code and its documentation change in the same commit.
- `04-ai/DECISIONS.md` is append-only in spirit: a reversed decision is superseded, not deleted.
- The feature file is the record of what was implemented, what was verified, and what was not.
- A stale document is treated as a defect with the same severity as a failing test.
