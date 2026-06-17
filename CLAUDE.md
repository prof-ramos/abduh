# CLAUDE.md

This file provides guidance to AI assistants (Claude Code and others) working in this repository.

## Repository Overview

**Repository:** prof-ramos/abduh
**Status:** New project — no source code, dependencies, or build configuration exist yet.
**Branch model:** Feature branches off `main`; current development branch is `claude/claude-md-docs-r2g09q`.

The project is in its initialization phase. Only an empty `README.md` and this `CLAUDE.md` are tracked. All architectural and tooling decisions are still open.

---

## Working in This Repository

### Branch Conventions

- `main` — stable, production-ready code; never commit directly
- Feature branches: `<type>/<short-description>` (e.g., `feat/user-auth`, `fix/login-redirect`)
- Claude Code branches: `claude/<task-slug>` (auto-created by the harness)

Always develop on the designated feature branch. Push to `origin/<branch>` before ending a session.

### Commit Messages

Follow Conventional Commits:

```
<type>(<scope>): <short summary>

[optional body]
```

Common types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `ci`

Keep the subject line under 72 characters. Reference issue numbers in the body when relevant.

### Pull Requests

- Open PRs against `main`
- PR title mirrors the commit subject line convention
- Include a summary, motivation, and test plan in the PR body
- Do not merge without CI passing (once CI is configured)

---

## Project Setup (to be populated)

> Update this section once the tech stack is chosen.

### Prerequisites

_None yet — list required runtimes, CLIs, and environment variables here._

### Install

```sh
# placeholder — replace with actual install command
```

### Development Server

```sh
# placeholder — replace with actual dev server command
```

### Build

```sh
# placeholder — replace with actual build command
```

### Tests

```sh
# placeholder — replace with actual test command
```

---

## Codebase Structure (to be populated)

> Update this section as directories and modules are created.

```
abduh/
├── README.md
└── CLAUDE.md
```

Expected layout (update as the project grows):

```
abduh/
├── src/            # application source
├── tests/          # test files mirroring src/ structure
├── public/         # static assets (if applicable)
├── docs/           # additional documentation
├── .github/
│   └── workflows/  # CI/CD pipelines
├── README.md
└── CLAUDE.md
```

---

## Key Conventions (to be finalized)

> Finalize these once the stack is chosen and document them here.

### Code Style

- Formatter and linter configs live at the repo root
- Run the formatter before committing; CI enforces it
- Prefer explicit over implicit; avoid magic

### Naming

- Files: `kebab-case` (e.g., `user-service.ts`)
- Classes/Types: `PascalCase`
- Functions/Variables: `camelCase`
- Constants: `SCREAMING_SNAKE_CASE`
- Tests: mirror source filename with `.test.<ext>` suffix

### Imports

- Prefer absolute imports over deep relative paths
- Group: external packages → internal modules → types → assets
- No circular dependencies

### Error Handling

- Validate at system boundaries (user input, external APIs); trust internal code
- Propagate errors with context; avoid swallowing exceptions silently

### Comments

- Only comment the *why*, not the *what*
- Document non-obvious constraints, workarounds, or subtle invariants

---

## Testing

> Update once a test framework is chosen.

- Unit tests live alongside source files or in a top-level `tests/` directory
- Every public function/API surface should have unit tests
- Integration tests cover cross-module or external-dependency boundaries
- Run the full test suite before opening a PR

---

## CI/CD (to be configured)

> Update once GitHub Actions (or equivalent) workflows are added.

Planned checks:
- Lint / format validation
- Type checking (if typed language)
- Unit test suite
- Build validation

---

## Environment Variables

> Document all required and optional env vars here once they exist.

| Variable | Required | Description |
|----------|----------|-------------|
| _(none yet)_ | — | — |

Use `.env.example` as the canonical template; never commit real secrets.

---

## AI Assistant Guidelines

These rules apply to all AI assistants (Claude Code and others) working in this repo.

### Do

- Read this file at the start of every session and re-read it after major project changes
- Prefer editing existing files over creating new ones
- Match the existing code style exactly — do not introduce new patterns without discussion
- Commit frequently with clear messages on the designated feature branch
- Push before the session ends so work is not lost
- When in doubt about scope, do less and ask rather than over-building

### Do Not

- Do not commit directly to `main`
- Do not add error handling, abstractions, or features beyond what the task requires
- Do not create documentation files (README, changelogs, etc.) unless explicitly requested
- Do not add comments that restate what the code obviously does
- Do not introduce new dependencies without noting them in the PR description
- Do not push to a branch other than the one designated for the current task
- Do not run destructive git commands (`reset --hard`, `push --force`, `branch -D`) without explicit user instruction

### Scope Discipline

Three similar lines of code is better than a premature abstraction. Fix the bug in front of you; do not refactor the surrounding code unless that is the task.

---

## Updating This File

Keep CLAUDE.md current as the project evolves:

- Add the real tech stack under **Project Setup** once dependencies are installed
- Fill in **Codebase Structure** as directories are created
- Finalize **Key Conventions** once linter/formatter configs exist
- Document env vars as they are introduced
- Update CI/CD section once workflows are added

This file is the authoritative guide for all AI assistants in this repository.
