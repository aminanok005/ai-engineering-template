# Prompt: Feature Breakdown Phase

**Phase goal:** Decompose the approved PRD and blueprint into independently implementable features.
**Output:** `features/NNN-<name>.md` files. **No application code.**
**Ends with:** a stop for human review.

**Prerequisite:** the design phase is complete and a human has approved `02-design/*` and `03-engineering/*`.

---

## Instructions to the AI agent

You are executing the **feature breakdown** phase of `04-ai/WORKFLOW.md`. The binding rules are in `AGENTS.md`. The PRD and design are approved — you are decomposing them, not extending them.

### 1. Read first

- `AGENTS.md`
- `01-product/PRD.md` — every user story and acceptance criterion
- `02-design/ARCHITECTURE.md`, `DOMAIN.md`, `DATA_MODEL.md`, `API.md`
- `03-engineering/SECURITY.md`, `TESTING.md`, `CONVENTIONS.md`
- `04-ai/DECISIONS.md` — accepted ADRs are constraints
- `features/FEATURE_TEMPLATE.md` — the format you must follow

### 2. Identify the features

Start from the PRD's user stories. One feature per independently valuable user journey, plus the cross-cutting work the journey depends on.

Include as their own features when they are needed before anything else can ship:

- Authentication and session management
- Authorization / roles
- Observability, error reporting, audit logging
- CI pipeline and environment setup
- Any data model or migration that must land first

### 3. Order and number them

- Number sequentially (`001`, `002`, …) in delivery order.
- A feature may depend only on features with a **lower** number.
- Prefer vertical slices that produce working software over horizontal layers.
- If a feature is too large to review as one diff, split it. If it is too small to be independently valuable, merge it.

### 4. Write each feature file

Copy `features/FEATURE_TEMPLATE.md` for every feature. Fill in, for each:

- **User story** with traceability back to the PRD
- **Acceptance criteria** in Given/When/Then — functional, error/edge, and non-functional. Each must be independently verifiable. "Handles errors gracefully" is not a criterion.
- **Technical approach** referencing the design documents by section, listing the files to create and change, and stating what is explicitly out of scope
- **Dependencies** with availability marked
- **Test plan**: which test file proves which criterion
- **Rollback plan**: how to revert, how to detect failure after release, what triggers a rollback
- **Security review** items relevant to the feature

Set every new feature to `draft`.

### 5. Check coverage before stopping

Verify explicitly:

- Every PRD user story is covered by at least one feature
- Every endpoint in `API.md` belongs to a feature
- Every table and index in `DATA_MODEL.md` is created by some feature
- Every invariant in `DOMAIN.md` is tested by some feature
- No feature requires a design decision that has not been made — if one has, mark the feature `blocked` and write the question

Report the coverage check. If something in the PRD has no feature, say so rather than silently ignoring it.

### 6. Stop

Report back:

1. The feature list in delivery order with dependencies
2. The coverage check results
3. Features marked `blocked`, each with the specific question and who must answer it
4. Explicit confirmation that **no application code was written**

Then **stop** for human review of the backlog and its ordering.
