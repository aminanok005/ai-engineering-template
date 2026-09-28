# Architecture Decision Records

> Purpose: record decisions that a future session — human or AI — must not re-litigate.
> Rules: ADRs are immutable. A decision that changes is superseded by a new ADR that references the old one.
> Convention: `ADR-NNN-kebab-case-title`

---

## ADR Index

| ADR | Title | Status | Date | Supersedes | Superseded by |
|-----|-------|--------|------|------------|---------------|
| [ADR-001](#adr-001-documentation-driven-workflow) | Documentation-driven workflow | Accepted | 2026-09-28 | — | — |
| [ADR-002](#adr-002-typed-end-to-end-with-explicit-boundaries) | Typed end-to-end with explicit boundaries | Accepted | 2026-09-28 | — | — |
| [ADR-003](#adr-003-source-of-truth-hierarchy) | Source-of-truth hierarchy | Accepted | 2026-09-28 | — | — |

**Status values:** `Proposed` → `Accepted` → (`Superseded by ADR-NNN` | `Deprecated`)

---

## Template

Copy this block for every new decision. Keep it short: a decision that needs more than a page usually hides two decisions.

```markdown
## ADR-NNN: <Short imperative title>

- **Status:** Proposed | Accepted | Superseded by ADR-NNN | Deprecated
- **Date:** YYYY-MM-DD
- **Deciders:** <names/roles>
- **Supersedes:** ADR-NNN (or None)
- **Superseded by:** (leave empty until superseded)
- **Feature:** features/NNN-<name>.md (if the decision arose from implementation)

### Context
What is true today, and what forces are at play? Facts and constraints only — no preference.

### Decision
The choice, stated in one or two sentences in the active voice: "We will …".

### Alternatives Considered

| Option | Why not |
|--------|---------|
| A | |
| B | |

### Consequences

**Positive**
- 

**Negative / costs**
- 

**Risks and mitigations**
- Risk: … → Mitigation: …

### Validation
How will we know later that this decision was wrong? Signal, threshold, and the review date.
```

---

## ADR-001: Documentation-Driven Workflow

- **Status:** Accepted
- **Date:** 2026-09-28
- **Deciders:** Template maintainers
- **Supersedes:** None
- **Superseded by:** —
- **Feature:** None (template-level)

### Context

AI coding agents produce large volumes of plausible code quickly. Without an external source of truth, they invent requirements, make architecture decisions implicitly, and produce codebases that no one fully understands or can safely modify. The cost is not the code — it is the loss of reviewability and maintainability.

### Decision

We will require that all AI-assisted work follows a document-first workflow: PRD → design → feature specification → implementation → tests → review, with a human approval gate at every phase transition. The repository is the source of truth; the conversation is not.

### Alternatives Considered

| Option | Why not |
|--------|---------|
| Prompt the agent once to build the whole system | Fastest to demo, slowest to maintain; no review points, no traceability |
| Conventional code-first workflow with AI autocomplete | Keeps the codebase the source of truth, but leaves requirements and architecture implicit and unversioned |
| Agent maintains a hidden plan file | Plans drift from reality and are not reviewable by the team |

### Consequences

**Positive**
- Every line of code traces to an approved document.
- Diffs stay small and reviewable.
- A new session — or a new teammate — can reconstruct the reasoning.

**Negative / costs**
- Slower to first code.
- Requires human review effort at four gates.
- Documents must be maintained, or they become misleading.

### Validation

Signal: features marked `completed` whose documentation is stale, or review comments that repeatedly ask for a missing rationale. Threshold: more than 2 such incidents per month. Review date: 2027-03-28.

---

## ADR-002: Typed End-to-End with Explicit Boundaries

- **Status:** Accepted
- **Date:** 2026-09-28
- **Deciders:** Template maintainers
- **Supersedes:** None
- **Superseded by:** —
- **Feature:** None (template-level)

### Context

Errors in AI-written code disproportionately appear at boundaries: HTTP payloads, database rows, queue messages, and configuration. Types that stop at the module boundary leave those seams unverified until runtime.

### Decision

We will enforce a type system that spans the stack — runtime validation at every untrusted boundary, static types across the domain, and generated or single-sourced types so that the API contract and the database model cannot drift from the code.

### Alternatives Considered

| Option | Why not |
|--------|---------|
| Types at the application core only | Leaves network, database, and configuration inputs unverified |
| Runtime validation only, no static types | Loses compile-time feedback exactly where refactoring happens |
| Independent type definitions per layer | Guarantees drift between the API contract, the database model, and the code |

### Consequences

**Positive**
- Boundary failures are caught at the edge with a clear error, not deep in the call graph.
- Contract drift is detected by the compiler and by tests.

**Negative / costs**
- Additional build tooling and a schema definition step.
- Validation code is boilerplate and must be generated or abstracted to stay cheap.

### Validation

Signal: production incidents traced to malformed or drifted boundary data. Threshold: any incident in a quarter. Review date: 2027-03-28.

---

## ADR-003: Source-of-Truth Hierarchy

- **Status:** Accepted
- **Date:** 2026-09-28
- **Deciders:** Template maintainers
- **Supersedes:** None
- **Superseded by:** —
- **Feature:** None (template-level)

### Context

A documentation-driven repository accumulates overlapping documents. Without a defined precedence, an agent resolves conflicts by guessing, and two documents end up stating opposite rules with equal apparent authority.

### Decision

We will define a strict precedence order — PRD, then design, then engineering standards, then AI workflow and decisions, then feature files, then `AGENTS.md` — and require every conflict resolution to be recorded as an ADR with the lower-priority document corrected.

### Alternatives Considered

| Option | Why not |
|--------|---------|
| All documents equal; agent reconciles case by case | Inconsistent across sessions; produces contradictory code |
| `AGENTS.md` as the single authority | Agent instructions become a second, undocumented requirements system |
| Most-recent-edit wins | Implicit, unreviewable, and rewards churn over correctness |

### Consequences

**Positive**
- Conflicts resolve deterministically.
- Every resolution is recorded and auditable.

**Negative / costs**
- Document edits propagate upward when a high-priority document changes.
- Maintaining the hierarchy is a discipline the team must keep.

### Validation

Signal: recurring conflicts between documents, or an ADR referencing a document that has since been rewritten. Threshold: any recurrence. Review date: 2027-03-28.

---

## Adding a New ADR

1. Copy the template above into a new `## ADR-NNN:` section in this file.
2. Add a row to the ADR Index.
3. Set **Status** to `Proposed` and stop for human review.
4. On approval, set **Status** to `Accepted` and record the decider and date.
5. To reverse an accepted decision, write a new ADR with **Supersedes: ADR-NNN**, set the old entry's status to `Superseded by ADR-NNN`, and correct any document the old decision had influenced.

Do not edit an accepted ADR to say something different from what it said when it was accepted.
