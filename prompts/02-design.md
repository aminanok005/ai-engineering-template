# Prompt: Design Phase

**Phase goal:** Produce the engineering blueprint.
**Output:** `02-design/*` and `03-engineering/*`. **No application code.**
**Ends with:** a stop for human review.

**Prerequisite:** the bootstrap phase is complete and a human has approved `01-product/PRD.md`.

---

## Instructions to the AI agent

You are executing the **design** phase of `04-ai/WORKFLOW.md`. The binding rules are in `AGENTS.md`. The PRD is approved; do not renegotiate product intent — if the design reveals a product gap, report it as an open question.

### 1. Read first

- `AGENTS.md`
- `01-product/PRD.md` — in full, including open questions
- `04-ai/WORKFLOW.md` and `04-ai/DECISIONS.md` — existing ADRs constrain your choices
- Any existing code in the repository — the design must fit what is already here, or propose a migration

### 2. `02-design/ARCHITECTURE.md`

- System boundary: what this system owns and what it delegates
- Technology stack, each choice justified and traceable to a requirement
- System components, each with responsibility, interfaces, data owned, failure mode, and scaling behavior
- Primary and asynchronous data flows, end to end
- Deployment: environments, topology, secrets delivery, migration-during-deploy, rollback
- Non-functional requirements, **each with a verification method** — an unverifiable requirement is not a requirement

Every component must trace to a PRD requirement. A component nothing requires does not go in the design.

### 3. `02-design/DOMAIN.md`

- Ubiquitous language glossary
- Bounded contexts, their responsibilities, and the context map
- Aggregates with the invariants each protects, its boundary, lifecycle, and concurrency strategy
- Entities, value objects, and the domain services that span them
- Domain events, their producers and consumers, and delivery guarantees
- A numbered list of invariants, each mapped to the test that enforces it

### 4. `02-design/DATA_MODEL.md`

- ERD
- Table definitions with every column typed, nullability explicit, and defaults stated
- Indexes, each naming the access pattern it serves
- Migration strategy: append-only, reversible or documented as irreversible, expand/migrate/contract for destructive changes
- The supported data access patterns with latency budgets — an unlisted query is not supported
- Data classification per table/column, aligned with `SECURITY.md`

### 5. `02-design/API.md`

- Endpoint table, then a contract per endpoint: request, validation rules, success and error responses
- Shared request/response schemas defined once
- Authentication mechanism and token handling
- Rate limits per scope with the key and the breach response
- One error envelope for every failure, with a message policy that never leaks internals
- Versioning and deprecation policy

### 6. `03-engineering/SECURITY.md`, `TESTING.md`, `CONVENTIONS.md`

Define these for the chosen stack:

- **Security:** authn/authz model, data protection by classification, input validation rules, OWASP Top 10 mitigations mapped to concrete controls, secrets management with owners and rotation, audit logging requirements
- **Testing:** test pyramid, unit/integration/E2E standards, test data rules, the CI gates that block a merge
- **Conventions:** language rules, project structure with dependency direction, naming, code organization, lint/format commands, git workflow

### 7. `04-ai/DECISIONS.md`

Every significant choice made in this phase gets an ADR using the file's template. Add a row to the index. Set status to `Proposed` and let the human accept it. Do not retro-edit an accepted ADR — supersede it.

### 8. Stop

Report back:

1. The design decisions you made and the requirements each traces to
2. The open questions that remain, each with who must answer it
3. The risks and trade-offs a reviewer should challenge first
4. Explicit confirmation that **no application code was written**

Then **stop** for human review. Do not begin feature breakdown, and do not start writing features.
