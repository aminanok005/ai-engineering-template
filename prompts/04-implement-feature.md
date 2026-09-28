# Prompt: Implement a Feature

**Phase goal:** Implement **exactly one** feature, end to end.
**Output:** code, tests, and updated documentation for that feature only.
**Ends with:** status `review`, and a stop for human review.

**Prerequisite:** the feature file exists with status `ready` or `implementing`, and a human has approved it.

---

## Instructions to the AI agent

You are implementing one feature from `features/NNN-<name>.md`. The binding rules are in `AGENTS.md`. **One feature per change set.** If the task you were given spans more than one feature, stop and say so.

### 1. Understand

Read, in full:

- The feature file — acceptance criteria, dependencies, test plan, rollback plan
- Every design document the feature references, by section
- `04-ai/DECISIONS.md` — accepted ADRs are constraints
- `03-engineering/CONVENTIONS.md` and `03-engineering/TESTING.md`
- The existing code this feature touches

State the feature's acceptance criteria back to the user. Confirm its dependencies are satisfied. If a dependency is missing, set the feature to `blocked` and stop.

### 2. Plan

Before writing code, write the plan:

- The approach, in the vocabulary of the design documents
- The files to create and the files to change, with the purpose of each
- The tests to add, mapped to the acceptance criteria they prove
- The risks or unknowns

Surface any conflict you find between the feature file and the design documents **now**, before writing code. If the conflict requires a decision, stop and raise it — do not resolve it yourself.

### 3. Implement

- Set the feature status to `implementing`.
- Write the **minimum** code that satisfies the acceptance criteria. Nothing beyond them.
- Follow `CONVENTIONS.md` exactly: structure, dependency direction, naming, organization.
- Respect `SECURITY.md`: authorize every new entry point, validate and bound every input, classify any new data, introduce no secret.
- No unrelated refactoring. No drive-by cleanups. Note them in the feature file instead.
- No placeholder implementations presented as complete work. A `TODO` is only allowed with a linked tracked item.
- If a new dependency seems necessary, stop — that is an ADR and a human decision, not an implementation detail.

### 4. Test

- Unit tests for every new behavior and every branch the feature introduces: happy path, boundary, failure.
- Integration tests for every new boundary the design documents name.
- E2E coverage only if the feature owns a critical user journey.
- Follow `TESTING.md`: test behavior, not implementation; AAA; no network, clock, or randomness in unit tests.
- **Never** weaken, delete, or skip a test to make a build pass. If a test fails, fix the code or report the blocker.

### 5. Validate

Run, and paste the real output of, each of:

```
<install>
<lint>
<format:check>
<typecheck>
<build>
<test>
```

Do not claim a check passed that you did not run. If a command is unavailable in this repository, say so instead of assuming a pass.

### 6. Review

Self-review against the definition of done in `AGENTS.md`, item by item:

- Every acceptance criterion met, and each one verified by a specific test
- Tests exist for the new behavior and pass
- No lint, typecheck, or build errors
- Security implications evaluated against `SECURITY.md`
- No scope creep beyond the acceptance criteria
- Any architectural deviation recorded as an ADR

Fix what you find. If something cannot be fixed without a decision, set the feature to `blocked` and write the question.

Update the feature file:

- Status → `review`
- Implementation log: what changed, what was verified, **what was not verified**
- Complete the security review checklist
- Record open questions and their owners

### 7. Update documentation

In the same change set, update the documents your change made inaccurate:

| Change | Document |
|--------|----------|
| New/changed component, flow, non-functional requirement | `02-design/ARCHITECTURE.md` |
| New/changed domain concept, aggregate rule, invariant | `02-design/DOMAIN.md` |
| New/changed table, index, access pattern, classification | `02-design/DATA_MODEL.md` |
| New/changed endpoint, schema, error case | `02-design/API.md` |
| New security surface | `03-engineering/SECURITY.md` |
| New testing pattern | `03-engineering/TESTING.md` |
| New coding pattern | `03-engineering/CONVENTIONS.md` |
| Architectural decision made during implementation | `04-ai/DECISIONS.md` — new ADR, `Proposed` |

Document changes and code changes ship together. A stale document is a defect, not a follow-up task.

### 8. Stop

Report back:

1. What you implemented, in one paragraph
2. What you **verified**, with the actual command output
3. What you **did not verify**, and why
4. Anything a reviewer should look at first
5. The feature file's new status

The status becomes `completed` only after a human reviews the result. Do not set it yourself.
