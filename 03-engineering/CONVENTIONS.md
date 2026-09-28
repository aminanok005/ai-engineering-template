# Coding Conventions

> Status: `draft`
> Related: `03-engineering/TESTING.md`
> Last updated: _YYYY-MM-DD_

Conventions exist so that code written by an AI agent reads like code written by the rest of the team. Where this file is silent, follow the existing codebase pattern.

---

## 1. Language-Specific Conventions

### TypeScript / JavaScript

- `strict: true`. `any` is not allowed; use `unknown` plus narrowing.
- Prefer `type` over `interface` for unions and mapped shapes; use `interface` for object contracts meant to be extended.
- All function parameters and return types are explicit at module boundaries. Internal helpers may infer.
- Prefer `const`; use `let` only when reassignment is intentional. Never `var`.
- No default exports except for framework-required entry points. Use named exports everywhere else.
- Enums are avoided in favor of `as const` objects with a union type.
- No floating-point arithmetic for money; use integer minor units.
- Async functions must handle rejection explicitly — no unhandled promise rejections.
- Imports: built-ins, external, internal, relative — in that order, separated by blank lines.
- Prefer pure functions at the core. Side effects (I/O, clock, randomness) are injected.

### Other languages

_Add a section per language used in this project (Python, Go, SQL, ...). The same rules apply: explicit types at boundaries, no unhandled errors, no global mutable state, deterministic tests._

---

## 2. Project Structure

```
<project root>/
├── src/
│   ├── modules/          # one directory per domain module/aggregate
│   │   └── <module>/
│   │       ├── <module>.domain.ts
│   │       ├── <module>.service.ts
│   │       ├── <module>.repository.ts
│   │       └── __tests__/
│   ├── shared/           # cross-cutting code with no domain knowledge
│   ├── config/           # typed, validated configuration
│   └── entrypoints/      # http handlers, workers, cron
├── tests/                # integration and e2e tests
├── scripts/              # seed, data generation, maintenance
├── docs/                 # only if a doc does not belong in 01-04 or features/
└── ...
```

Rules:

- **Dependency direction:** entrypoints → services → domain. The domain layer imports nothing from the layers above it. Dependencies point inward.
- A module may not import another module's internals; cross-module access goes through the public surface of that module.
- `shared/` must stay domain-agnostic. If it needs to know about a domain, it belongs in that domain.
- Business logic does not live in route handlers, resolvers, or UI components.
- No file exceeds a length that makes it unreadable in review (guideline: ~400 lines). Split before exceeding it.

---

## 3. Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| File | `kebab-case`, suffix by role | `user-service.ts`, `user.repository.ts` |
| Component / class | `PascalCase` | `UserService` |
| Function / variable | `camelCase` | `createUser` |
| Constant | `SCREAMING_SNAKE_CASE` | `MAX_UPLOAD_BYTES` |
| Database table / column | `snake_case`, plural table | `users`, `created_at` |
| Type | `PascalCase` | `UserCreated` |
| Enum member | `SCREAMING_SNAKE_CASE` | `Status.ACTIVE` |
| Test file | mirrors the source path | `user-service.spec.ts` |
| Feature file | `NNN-kebab-case.md` | `001-authentication.md` |
| ADR | `ADR-NNN-kebab-case` | `ADR-001-use-postgres.md` |
| Environment variable | `SCREAMING_SNAKE_CASE` | `DATABASE_URL` |

Rules:

- Names describe the domain concept, not the implementation (`invoiceTotal`, not `calcSum`).
- Booleans read as predicates: `isActive`, `hasExpired`, `canRefund`.
- No abbreviations except domain terms already in the ubiquitous language.
- No `util`, `helper`, `manager`, `base`, or `common` names in new code — name for the responsibility.

---

## 4. Code Organization

- **One responsibility per module.** A file that needs "and" in its description is two files.
- Keep the public surface of a module small; internal helpers stay private.
- Functions are short enough to read without scrolling (guideline: ≤ 40 lines). Extract when longer.
- **Early return over nested conditionals.**
- Prefer pure transformations and explicit data flow over hidden shared state.
- Configuration is read once, validated at startup, and injected. No `process.env` access deep in the call graph.
- Errors carry a machine-readable `code` and a safe `message`. Stack traces never reach clients (see `02-design/API.md`).
- Comments explain **why**, not **what**. The code states what it does.
- Public functions, exported types, and non-obvious invariants are documented. The rest is not.

---

## 5. Linting and Formatting

- Formatter config is committed (e.g. Prettier) and formatting is never hand-debated. `pnpm format` applies it.
- Linter config is committed (e.g. ESLint) with the type-aware ruleset enabled.
- Rules enforced (all blocking):
  - no `any`, no unused variables, no floating promises
  - no direct `console.log` outside a logging abstraction
  - no `process.env` outside `config/`
  - no raw SQL string interpolation
  - no TODO comment without a linked tracked item
- Lint suppressions require a comment stating the reason and are reviewed like any other code.
- Pre-commit hooks run the formatter and fast lints. CI runs the full blocking set.

---

## 6. Git Workflow

- **One feature per change set.** A pull request maps to one feature file.
- Branch naming: `feature/001-authentication`, `fix/…`, `chore/…`, `docs/…`.
- Commit messages follow Conventional Commits: `type(scope): summary`, where `type` ∈ `feat|fix|docs|refactor|test|chore|build|ci`.
  - Example: `feat(auth): add password reset request flow`
- The commit subject is imperative, ≤ 72 characters, and explains *why* in the body when it is not obvious.
- A commit that changes behavior updates the relevant documentation in the same commit (`AGENTS.md` rule 8).
- `main` is always releasable. Feature work merges through pull request with the CI gates green.
- **Never** force-push to a shared branch, rewrite published history, or commit directly to `main`.
- Secrets never appear in commits; see `03-engineering/SECURITY.md` section 5.
