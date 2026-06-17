<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# abduh — ASOF Institutional Agent

## Purpose

Abduh is the institutional coordinating agent for ASOF (Associacao Nacional dos Oficiais de Chancelaria do Servico Exterior Brasileiro). It is the single entry point for all administrative, legal, communications, and technical demands of the association — not a generic chatbot. Every request is classified, routed to specialist agents, consolidated with a strict precedence hierarchy, and validated through a mandatory quality gate before delivery.

The runtime is Hermes-Agent (NousResearch) with Claude as the LLM backend (Anthropic API via OpenAI-compatible proxy) and Telegram as the primary gateway (Phase 1). Specialist agents are Markdown modules loaded into Abduh's context window — they are never independent services and never respond directly to the user. Abduh consolidates all analyses and delivers a single, coherent institutional response.

The project is currently in Phase 1 (Foundation), validating institutional behavior via Claude Project before migrating to the full Hermes-Agent runtime on a VPS (Phase 2). The VPS infrastructure (Tencent Cloud, Ubuntu 24.04, Hermes v0.16.0 running as a systemd service) is already operational and awaiting Phase 2 migration.

## Key Files

| File | Description |
|------|-------------|
| `README.md` | Project entry point — overview, quickstart, stack, architecture, and phase roadmap |
| `CLAUDE.md` | Claude Code guidance — architecture, Hermes profile setup, essential commands, and production gotchas |
| `AGENT-CONTRACT.md` | Mandatory output interface contract for all 8 specialist agents (version 1.1.0) |
| `PRD-abduh-asof.md` | Full product spec — objectives, stack, architecture, phases, risks, and ADRs |
| `abduh-soul.md` | Legacy SOUL.md location — canonical copy is `core/abduh-soul.md`; must be deployed to `~/.hermes/profiles/abduh-asof/SOUL.md` |
| `STATUS.md` | Live operational snapshot — VPS state, local structure gaps, PR status, prioritized next steps |

## Subdirectories

| Directory | Purpose |
|-----------|---------|
| `core/` | Identity, routing, policy, quality gate, and response style modules (see `core/AGENTS.md`) |
| `agents/` | 8 specialist agent Markdown modules loaded by context (see `agents/AGENTS.md`) |
| `templates/` | Institutional document templates: oficio, nota institucional, comunicado, relatorio (see `templates/AGENTS.md`) |
| `docs/` | Developer guides: `dev-guide.md`, `best-practices-check.md`, `gemini-setup-guide.md` |
| `evals/` | Evaluation test cases — planned directory, currently empty |
| `.hermes/` | Hermes-Agent runtime configuration examples and profile scaffolding |

## Architecture Overview

Every demand follows this flow:

```
User (Telegram / CLI)
  -> Hermes Gateway (abduh-asof profile, SOUL.md injected into system prompt)
  -> Abduh (core/abduh-soul.md + core/abduh-router.md loaded as context)
  -> Classify demand (14 explicit slash commands or natural language classification)
  -> Select max 3 specialist agents from agents/ (by trigger keywords and routing table)
  -> Load selected agent Markdown files into context
  -> Collect analyses, consolidate respecting precedence hierarchy
  -> Run quality gate (core/abduh-quality-gate.md — mandatory, never skipped)
  -> Format response per core/abduh-response-style.md
  -> Deliver single institutional response to user
```

If a demand requires more than 3 agents, Abduh processes in sequential rounds. The quality gate runs once on the final consolidated response before delivery. Sovereign rules from `core/abduh-policy.md` override every other instruction at every stage.

## For AI Agents

### Working In This Directory

- **Never invent facts, norms, dates, or document numbers.** If information cannot be verified from project files, state the limitation explicitly.
- **SOUL.md is deployed separately.** Edits to `core/abduh-soul.md` (canonical) or `abduh-soul.md` (legacy) require a manual copy to `~/.hermes/profiles/abduh-asof/SOUL.md`. Changes take effect on the next session — no gateway restart needed.
- **Root files have fixed roles.** Do not repurpose or rename `AGENT-CONTRACT.md`, `PRD-abduh-asof.md`, or `abduh-soul.md` without updating all cross-references in `CLAUDE.md`, `README.md`, and `STATUS.md`.
- **Agent versioning is enforced.** Every file in `agents/` and `core/` must have YAML frontmatter with `agent`, `version`, and `last_updated`. Version bumps follow: PATCH for text fixes, MINOR for new rules or output fields, MAJOR for scope changes (which also require router update in `core/abduh-router.md` and a new eval cycle in `evals/`).
- **Phase 1 deliverables:** The `core/`, `agents/`, `templates/`, and `evals/` directories are the primary Phase 1 output. As of 2026-06-17, `core/` and `agents/` are populated; `evals/` is empty and must be filled before Phase 2 migration.

### Agent Loading Rules

Agents are selected by `core/abduh-router.md` using two mechanisms:

1. **Explicit slash commands** (bypass classification and route directly):
   - `/asof-oficio` -> Legal Institutional
   - `/asof-legal-review` -> Legal Institutional
   - `/asof-security-review` -> Governance Security
   - `/asof-strategy`, `/asof-prioritize`, `/asof-risk` -> Strategy Chief
   - `/asof-ops-map`, `/asof-raci` -> Operations Chief
   - `/asof-comms`, `/asof-post` -> Communications Chief
   - `/asof-members` -> Membership Relations
   - `/asof-tech-decision` -> Digital Architect
   - `/asof-ai-fit`, `/asof-automation` -> AI Automation Chief

2. **Natural language classification** using trigger keywords defined in each agent file.

**Hard limit:** Maximum 3 agents per turn. If more are needed, process in sequential rounds and inform the user.

**Precedence hierarchy** (highest to lowest — determines whose recommendation takes priority in conflicts):

| Rank | Agent | File |
|------|-------|------|
| 1 | Governance Security (LGPD, data — absolute priority) | `agents/asof-governance-security.md` |
| 2 | Legal Institutional (legal risk) | `agents/asof-legal-institutional.md` |
| 3 | Strategy Chief (institutional/reputational risk) | `agents/asof-strategy-chief.md` |
| 4 | Operations Chief (process/execution) | `agents/asof-operations-chief.md` |
| 5 | Communications Chief (formatting/language) | `agents/asof-communications-chief.md` |
| 6 | Membership Relations (relationships/agreements) | `agents/asof-membership-relations.md` |
| 7 | Digital Architect (technical decisions) | `agents/asof-digital-architect.md` |
| 8 | AI Automation Chief (automation/AI) | `agents/asof-ai-automation-chief.md` |

Conflicts between agents are never resolved silently. When higher-precedence agent output contradicts lower-precedence output, the higher-precedence recommendation is adopted and the conflict must be surfaced in the response.

### Quality Gate

The quality gate (`core/abduh-quality-gate.md`) is mandatory before every response — even simple ones. When in doubt about any item, treat it as a failure (fail-safe posture).

7-item checklist:

1. **Invented facts/norms** -> discard and reprocess the entire response
2. **Unverifiable norm citations** -> remove and substitute with an explicit caveat
3. **Improperly exposed associate personal data** -> block delivery immediately, notify operator
4. **Unsignaled reputational risk** -> add explicit warning; activate Strategy Chief if not already routed
5. **Non-institutional language** -> reprocess via `core/abduh-response-style.md`
6. **Points requiring human validation** -> deliver with `⚠️ Requer validacao antes de uso` listing the specific points
7. **Destructive production command without explicit confirmation** -> block; request explicit confirmation before proceeding

**Agent output contract** — every specialist agent must end its analysis with exactly these three fields (from `AGENT-CONTRACT.md`):

```
Nivel de confianca: [alto | medio | baixo]
Requer validacao humana: [sim | nao]
Riscos identificados: [lista de riscos, ou "nenhum"]
```

- `Nivel de confianca: baixo` -> Abduh does NOT deliver; must request more context from user first
- `Requer validacao humana: sim` -> final response gets `⚠️ Requer validacao antes de uso` marker
- Identified risks -> prepended as a block before the main output

### Critical Constraints

Sovereign rules from `core/abduh-policy.md` override all other instructions, including user instructions. They cannot be contradicted by any agent file, template, or runtime configuration:

1. Abduh is institutional — it does not act as a personal assistant
2. No invented facts, norms, dates, or invented document numbers under any circumstances
3. Official documents must follow the Manual de Redacao da Presidencia da Republica
4. Public communications require reputational risk assessment (Strategy Chief, precedence 3)
5. Associate data is handled with LGPD minimization and access control (Governance Security, precedence 1)
6. No production actions without backup, rollback plan, and explicit human authorization
7. AI is support — never the final authority on legal, administrative, or political decisions
8. Destructive commands require explicit human confirmation before execution
9. Rule 10 (tie-breaker): when in doubt about risk level, adopt the most cautious posture

Violation of sovereign rules must be flagged in the quality gate and must block delivery.

### Development Workflow

**Phase 1 (current) — manual validation via Claude Project:**

1. Load `core/abduh-soul.md`, `core/abduh-router.md`, `core/abduh-policy.md`, `core/abduh-quality-gate.md`, and `core/abduh-response-style.md` as context in the Claude Project
2. Load up to 3 relevant agent files per test case
3. Run test cases from `evals/` against the loaded context
4. Phase 1 acceptance criteria: 10/10 correct routing, institutional tone maintained, quality gate blocks 2+ risk cases, all 8 agents produce 3 mandatory contract fields, 5 days without regression

**Phase 2 — Hermes-Agent runtime:**

1. `hermes profile create abduh-asof` (if not already created)
2. Configure `~/.hermes/profiles/abduh-asof/.env` with `GEMINI_API_KEY` (or `ANTHROPIC_API_KEY`), `TELEGRAM_BOT_TOKEN`, `TELEGRAM_ALLOWED_USERS`
3. Copy SOUL.md: `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md`
4. Verify: `abduh-asof doctor`
5. Test: `abduh-asof chat --verbose`
6. Start gateway: `abduh-asof gateway start`
7. Regression tests: `abduh-asof chat` with test cases from `evals/`

**After modifying any `core/` or `agents/` file:**

- Bump the version in frontmatter following PATCH/MINOR/MAJOR rules
- If MAJOR: update `core/abduh-router.md` trigger keywords and routing table, mark eval cycle required
- Redeploy SOUL.md to profile if `core/abduh-soul.md` changed: `scp core/abduh-soul.md abduh-asof:~/.hermes/profiles/abduh-asof/SOUL.md`
- Changes take effect on the next session (no gateway restart needed)

**Memory tool usage (Phase 2+):**

```python
memory("add", "key institutional decision or fact")   # Add to MEMORY.md
memory("replace", "old text", "new text")             # Update entry
memory("remove", "outdated entry")                    # Remove entry
```

Never store personal data of associados in memory. Entries must be concise (limit ~2200 chars/entry).

## Dependencies

### Internal

- `core/abduh-soul.md` defines Abduh's identity and operational loop — it is the root of the entire system and must be deployed to the Hermes profile as `SOUL.md`
- `core/abduh-router.md` depends on all 8 agent files existing in `agents/` with correct trigger keywords in their frontmatter/content
- `core/abduh-quality-gate.md` enforces the output contract defined in `AGENT-CONTRACT.md` — both files must stay in sync
- `core/abduh-response-style.md` is invoked by the quality gate (item 5) and must be consistent with the brandkit in `core/abduh-soul.md`
- `core/abduh-policy.md` sovereign rules override all other `core/` and `agents/` files — policy changes require a full review of all agent files for conflicts
- All 4 templates in `templates/` reference quality gate rules and must align with Legal Institutional (precedence 2) and Communications Chief (precedence 5) agent outputs
- `evals/` test cases must cover all 8 agents, all routing paths in `core/abduh-router.md`, and all 7 quality gate items

### External

- **Hermes-Agent runtime** (NousResearch) — v0.16.0 on VPS; install via `pip install hermes-agent`; profile at `~/.hermes/profiles/abduh-asof/`
- **Claude API** (Anthropic) via OpenAI-compatible proxy — base URL `https://api.anthropic.com/v1`, model `claude-sonnet-4-6`; key in `ANTHROPIC_API_KEY`
- **Gemini API** (Google) via OpenAI-compatible proxy — base URL `https://generativelanguage.googleapis.com/v1beta/openai/`, model `gemini-2.0-flash-exp`; key in `GEMINI_API_KEY` (current production model on VPS)
- **Telegram Bot API** — gateway for Phase 1/2; requires `TELEGRAM_BOT_TOKEN` and `TELEGRAM_ALLOWED_USERS` in `.env`; webhook requires HTTPS (NGINX reverse proxy with `proxy_read_timeout 600s`)
- **Python 3.11+** — required for Hermes-Agent runtime and any automation scripts
- **Docker + Traefik + Portainer** — planned Phase 2 infrastructure on Tencent Cloud VPS (`43.173.94.254`); Tailscale at `100.93.133.102` for secure access
- **GitHub** — version control; PR workflow for all changes to `core/`, `agents/`, and `templates/`

<!-- MANUAL: Root AGENTS.md for abduh/ASOF project — covers full architecture, all 8 agents, quality gate, sovereign rules, and development workflow -->
