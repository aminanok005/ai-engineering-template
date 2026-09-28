# Prompt: Bootstrap Phase

**Phase goal:** Establish the documentation foundation from the PRD.
**Output:** Documents only. **No application code.**
**Ends with:** a stop for human review.

---

## Instructions to the AI agent

You are executing the **bootstrap** phase of the workflow in `04-ai/WORKFLOW.md`. The binding rules are in `AGENTS.md`. Read both before doing anything else.

### 1. Read first

- `AGENTS.md` — the binding engineering rules
- `01-product/PRD.md` — the product intent
- `README.md` — the template's philosophy and structure
- The existing repository — `ls`, read what is there, note what already exists

Do not ask the user to repeat information that is already written down.

### 2. Assess the current state

Produce a short assessment (in your reply, and as notes in the PRD) of:

- Which documents exist and which are still empty or placeholder
- Which PRD sections are incomplete
- Where the PRD and the existing code disagree
- Which questions must be answered before design can start

### 3. Create the documentation foundation

Ensure this structure exists, creating any missing file from the templates in this repository:

```
AGENTS.md
README.md
.gitignore
01-product/PRD.md
02-design/ARCHITECTURE.md
02-design/DOMAIN.md
02-design/DATA_MODEL.md
02-design/API.md
03-engineering/SECURITY.md
03-engineering/TESTING.md
03-engineering/CONVENTIONS.md
04-ai/WORKFLOW.md
04-ai/DECISIONS.md
features/FEATURE_TEMPLATE.md
prompts/01-bootstrap.md
prompts/02-design.md
prompts/03-feature-breakdown.md
prompts/04-implement-feature.md
```

Populate each document with what is already known:

- Copy the known requirements from the PRD into the relevant design sections as **drafts marked with open questions**.
- Leave sections as explicit `draft` / `_TODO_` markers where the information genuinely does not exist yet. **Do not invent it.**
- Record every assumption you had to make in the PRD's *Constraints and Assumptions* and *Open Questions* sections, with an owner.

### 4. Verify `.gitignore`

Read `.gitignore` and confirm that environment files, secrets, build output, dependencies, and local databases are excluded. If a secret-bearing pattern is missing, add it and report the change. Never create or commit an actual secret.

### 5. Stop

Report back:

1. What documents now exist and which remain incomplete
2. The open questions, each with who must answer it
3. Explicit confirmation that **no application code was written**

Then **stop**. Do not begin the design phase. Do not propose architecture. The next step requires human approval of the PRD.
