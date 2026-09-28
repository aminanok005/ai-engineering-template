# AGENTS.md

Global engineering rules for AI agents working in this repository.

These rules are binding. If an instruction in a prompt or a feature file conflicts with this file, **this file wins**. If a task appears to require violating a rule, stop and report the conflict instead of proceeding.

---

## 1. The Core Rule

> The AI agent is an implementation engine operating under documented engineering constraints, **not an autonomous architect**.

The agent does not invent product requirements, does not silently change architecture, and does not create subsystems that no document describes. Every piece of code must trace back to an approved document in this repository.

---

## 2. Source of Truth Hierarchy

Read in this order. Later files are subordinate to earlier files.

| Priority | Concern | File |
|----------|---------|------|
| 1 | Product intent | `01-product/PRD.md` |
| 2 | Architecture, domain, data, API | `02-design/*.md` |
| 3 | Security, testing, conventions | `03-engineering/*.md` |
| 4 | AI workflow and decisions | `04-ai/WORKFLOW.md`, `04-ai/DECISIONS.md` |
| 5 | Feature specification | `features/<id>-<name>.md` |
| 6 | This file | `AGENTS.md` |

When two documents disagree, the higher-priority document wins. Record the resolution as an ADR in `04-ai/DECISIONS.md` and fix the lower-priority document.

---

## 3. Non-Negotiable Rules

1. **Never implement features outside a `features/*.md` specification.** If no feature file exists for the work, stop and request one.
2. **Never invent requirements.** Ambiguity is reported, not guessed. Use the `blocked` status and list open questions in the feature file.
3. **Never change architecture, add dependencies, or modify data models without an ADR** in `04-ai/DECISIONS.md` and explicit human approval.
4. **Never skip the review gates.** Each phase in `04-ai/WORKFLOW.md` ends with a stop-for-review point.
5. **Never commit secrets, credentials, tokens, or real user data.** Read `.gitignore` before adding files.
6. **Never weaken, delete, or skip a failing test to make a build pass.** Fix the code or report the blocker.
7. **Never write code that is not covered by the feature's acceptance criteria.**
8. **Never leave documentation stale.** A change to code and its documentation ship together in the same commit.
9. **Never add placeholder/TODO implementations presented as complete work.** Use explicit `TODO` comments tied to a tracked item, or leave the code out.
10. **Never refactor beyond the current feature scope.** Unrelated cleanups are proposed in the feature file's notes, not done inline.

---

## 4. Phase Rules

### Bootstrap phase
- Read the PRD and the repository.
- Establish the documentation foundation only.
- **Do not** write application code, scaffolding, or config for runtime behavior.

### Design phase
- Produce `ARCHITECTURE.md`, `DOMAIN.md`, `DATA_MODEL.md`, `API.md`, `SECURITY.md`, `TESTING.md`, `CONVENTIONS.md`.
- **Do not** write application code.
- **Stop** for human review.

### Feature breakdown phase
- Decompose the PRD into independently implementable feature files in `features/`.
- Each feature must be independently testable and independently releasable (or explicitly marked as depending on an earlier feature).
- **Do not** write application code.
- **Stop** for human review.

### Implementation phase
- Exactly one feature at a time.
- Follow the feature lifecycle: `draft → ready → implementing → review → completed` (or `blocked`).
- Follow the loop in `prompts/04-implement-feature.md`: Understand → Plan → Implement → Test → Validate → Review → Update docs.
- Update the feature file's status as the work progresses.

---

## 5. Definition of Done

A feature is `completed` only when all of the following are true:

- [ ] Every acceptance criterion in the feature file is met.
- [ ] Unit tests exist for the new behavior and pass.
- [ ] Integration tests exist for new boundaries and pass.
- [ ] No lint, typecheck, or build errors.
- [ ] Security implications were evaluated against `03-engineering/SECURITY.md`.
- [ ] Relevant docs in `02-design/` and `03-engineering/` are updated.
- [ ] Any architectural deviation is recorded as an ADR.
- [ ] The feature file lists the files created/changed.
- [ ] A human has reviewed the result.

---

## 6. Communication Rules

- Report status in the feature file, not only in chat. Chat is ephemeral; the repository is the record.
- State what you did, what you verified, and what you did not verify.
- Distinguish clearly between **implemented**, **assumed**, and **unverified**.
- When blocked, name the specific question and who must answer it.
- Do not claim success based on code that has not been run or tested.

---

## 7. Change Discipline

- One feature per change set. Keep diffs reviewable.
- Match the style, naming, and structure defined in `03-engineering/CONVENTIONS.md`.
- If the conventions file is silent, follow the existing codebase pattern.
- Update `04-ai/DECISIONS.md` for decisions that a future session should not re-litigate.

---

## 8. Repository Map

```
ai-engineering-template/
├── AGENTS.md              # These rules (binding)
├── README.md              # Template philosophy and workflow
├── 01-product/            # What we are building and why
│   └── PRD.md
├── 02-design/             # How the system is built
│   ├── ARCHITECTURE.md
│   ├── DOMAIN.md
│   ├── DATA_MODEL.md
│   └── API.md
├── 03-engineering/        # How the code is written and verified
│   ├── SECURITY.md
│   ├── TESTING.md
│   └── CONVENTIONS.md
├── 04-ai/                 # How the agent works
│   ├── WORKFLOW.md
│   └── DECISIONS.md
├── features/              # Unit of implementation
│   └── FEATURE_TEMPLATE.md
└── prompts/               # Reusable phase prompts
    ├── 01-bootstrap.md
    ├── 02-design.md
    ├── 03-feature-breakdown.md
    └── 04-implement-feature.md
```

Keep project-specific knowledge in the project documents. Keep AI behavior in `AGENTS.md` and `04-ai/`. Keep reusable workflows in `prompts/`. Keep feature requirements in `features/`. This separation prevents AI instructions from becoming a second, undocumented requirements system.
