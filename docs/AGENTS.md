<!-- Parent: ../AGENTS.md -->
<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# docs

## Purpose

This directory contains developer documentation for the Abduh/ASOF project. It covers the full developer lifecycle: environment setup and Hermes-Agent profile configuration, best practices audit for Phase 2 production readiness, and step-by-step provider configuration guides (Gemini, Claude, OpenAI). These files are the authoritative reference for engineers setting up, maintaining, or migrating the `abduh-asof` Hermes profile, as well as for understanding the gap analysis between current implementation and production requirements.

## Key Files

| File | Description |
|------|-------------|
| `dev-guide.md` | Primary developer reference: setup, architecture, workflow, testing, troubleshooting, and quick-reference command table |
| `best-practices-check.md` | Audit report (generated 2026-06-17) of implemented vs. missing practices across Hermes, Claude/Gemini API, Telegram, Docker, security, monitoring, backup, and performance — includes Phase 2 readiness checklist |
| `gemini-setup-guide.md` | Step-by-step guide for configuring Gemini as the LLM backend via the OpenAI-compatible proxy (`https://generativelanguage.googleapis.com/v1beta/openai/`) |

## For AI Agents

### Working In This Directory

- Do NOT invent or fabricate configuration values, version numbers, API endpoint URLs, or command syntax. Every value must be traceable to the source files or official provider documentation.
- When modifying `dev-guide.md`, preserve the exact command syntax in code blocks. These commands are executed verbatim by developers and errors have direct production impact.
- When updating `best-practices-check.md`, treat the "Critical" tier items in the Phase 2 readiness checklist as blocking — do not downgrade their priority without explicit human authorization.
- When modifying `gemini-setup-guide.md`, always validate that the base URL (`https://generativelanguage.googleapis.com/v1beta/openai/`), model names, and env var names (`GEMINI_API_KEY`) match what is documented in the Hermes profile config.
- Agent versioning rules apply to all agent files edited based on guidance here: PATCH for text fixes, MINOR for new rules or output fields, MAJOR for scope changes (requires router update and new eval cycle).
- SOUL.md is not in this directory but is referenced heavily. After any update to `core/abduh-soul.md`, the deploy command must be noted: `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md`. Changes take effect on next session only.
- Do not store or reference personal data of associados in documentation examples. All examples must use placeholder values.

### Documentation Standards

- All code snippets must be complete and executable — no pseudocode or partial fragments in setup guides.
- Configuration values that require secrets (API keys, bot tokens) must always reference `.env` variables, never hardcoded values.
- The `.env` file must always carry a `chmod 600` reminder whenever it is mentioned.
- Model names are case-sensitive and must match the provider registry exactly. Current production model for Gemini: `gemini-2.0-flash-exp`. Claude alternative: `claude-sonnet-4-6` with `provider: anthropic`.
- Any table listing "implemented vs. gaps" must be kept current with the actual state of the codebase; mark items as complete only after verification.
- Phase status labels (Phase 1 Foundation, Phase 2 Hermes Runtime, etc.) must remain consistent with `STATUS.md` and `README.md`.
- Troubleshooting tables must include the exact error message or symptom, cause, and resolution command.

## Dependencies

### Internal

- `../CLAUDE.md` — Project-level instructions for Claude Code; defines architecture, agent precedence, Hermes profile structure, and production gotchas referenced throughout these docs.
- `../README.md` — Project entry point; documentation index table and phase roadmap must stay in sync with descriptions here.
- `../STATUS.md` — Live operational snapshot; Phase 1 next steps and missing directories (`core/`, `agents/`, `templates/`, `evals/`) inform the scope of work described in `dev-guide.md`.
- `../abduh-soul.md` (source for `~/.hermes/profiles/abduh-asof/SOUL.md`) — Identity and sovereign rules; `dev-guide.md` documents how to deploy updates.
- `../AGENT-CONTRACT.md` — Mandatory output contract for all 8 specialist agents; referenced in `dev-guide.md` testing section.
- `../PRD-abduh-asof.md` — Full product spec; architectural decisions documented here must align with ADRs and phases described there.
- `../.hermes/profiles/abduh-asof/config.example.yaml` — Reference configuration for the Hermes profile; `gemini-setup-guide.md` and `dev-guide.md` derive config snippets from this file.
- `../templates/` — Official correspondence and communication templates; produced by agents following quality gates described in `dev-guide.md`.

<!-- MANUAL: Review this file after any changes to STATUS.md, dev-guide.md, or gemini-setup-guide.md to keep cross-references accurate. -->
