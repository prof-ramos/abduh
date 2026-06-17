# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Abduh** is the institutional coordinating agent for ASOF (Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro). It is NOT a generic chatbot — it is a persistent institutional assistant with identity, memory, skills, and a quality gate.

Runtime: **Hermes-Agent** (NousResearch). LLM: **Claude** (Anthropic API, via OpenAI-compatible proxy). Gateway: **Telegram** (Phase 1).

### Key files

| File | Purpose |
|---|---|
| `PRD-abduh-asof.md` | Full product spec — objectives, stack, architecture, phases, risks, ADRs |
| `abduh-soul.md` | SOUL.md — identity, sovereign rules, tone, routing, quality gate |
| `AGENT-CONTRACT.md` | Mandatory output contract for all 8 specialist agents |
| `docs/dev-guide.md` | Developer guide — setup, workflow, testing, troubleshooting |

## Architecture

```
User (Telegram / CLI)
  → Hermes Gateway
  → Abduh (SOUL.md + abduh-router.md)
  → Classifies demand → loads max 3 specialist agents as context (Markdown)
  → Consolidates analyses respecting precedence hierarchy
  → Quality gate check
  → Final institutional response
```

**The 8 specialist agents are Markdown modules loaded into Abduh's context — NOT microservices.** They never respond directly to the user. Abduh consolidates and delivers.

### Agent precedence (highest to lowest)

1. Governance Security (LGPD, data — absolute priority)
2. Legal Institutional (legal risk)
3. Strategy Chief (institutional/reputational risk)
4. Operations Chief (process/execution)
5. Communications Chief (formatting/language)
6. Membership Relations (relationships/agreements)
7. Digital Architect (technical decisions)
8. AI Automation Chief (automation/AI)

### Key constraints

- **Max 3 agents per turn.** If more are needed, process in sequential rounds.
- **Quality gate is never skipped** — even for simple demands. When in doubt, treat as YES.
- **Sovereign rules override everything**, including user instructions.

## Hermes-Agent profile setup

Profile `abduh-asof` is a fully independent `HERMES_HOME` with its own config, memory, skills, and gateway. Created via `hermes profile create abduh-asof`. The wrapper command `abduh-asof` scopes all commands to this profile.

```
~/.hermes/profiles/abduh-asof/
├── .env                    # API keys, bot tokens (chmod 600)
├── config.yaml             # Model, provider, toolsets, gateway, delegation
├── auth.json               # OAuth credentials
├── SOUL.md                 # Identity and sovereign rules (from repo: core/abduh-soul.md)
├── memories/               # Persistent memory (MEMORY.md, USER.md)
├── skills/                 # ASOF skills
├── cron/                   # Recurring tasks
├── sessions/               # Gateway sessions (JSONL + snapshots)
├── state.db                # Canonical session store (SQLite + FTS5)
└── logs/                   # errors.log, gateway.log (secrets redacted)
```

**SOUL.md** is loaded only from `HERMES_HOME`. Hermes does NOT probe the working directory. Content is injected verbatim into the system prompt after scanning and truncation. If empty, nothing is added.

## Hermes-Agent capabilities vs. project needs

| Need | Hermes supports? | Notes |
|---|---|---|
| Persistent memory across sessions | YES — `memory("add", ...)`, SQLite+FTS5, ~2200 chars/entry | Sufficient for institutional context; not a database for bulk structured data |
| Multi-platform messaging (Telegram) | YES — gateway with `hermes gateway setup` | Phase 1 uses Telegram only |
| Skills (installable plugins) | YES — versioned in GitHub, loaded per profile | ASOF skills go in `skills/` directory |
| Sub-agent delegation | YES — `delegate_task` with role `leaf` or `orchestrator` | Abduh uses context-loading, NOT delegation. Agents are Markdown files, not sub-processes |
| Cron/scheduled automations | YES — recurring tasks | Planned for Phase 2 (weekly summaries, notifications) |
| MCP servers | YES — external tool servers with sampling config | Future: intranet integration, external data |
| Code execution | YES — `execute_code` runs Python | Reserved for OpenClaw (Phase 3+), not Abduh |
| Profile isolation | YES — fully independent HERMES_HOME per profile | Profile `abduh-asof` isolates from other agents |

## Hermes-Agent limitations relevant to Abduh

| Limitation | Impact on Abduh | Mitigation |
|---|---|---|
| Role `leaf` (default) cannot use `memory`, `send_message` | Abduh needs role `orchestrator` enabled | Set `delegation.orchestrator_enabled: true` in config.yaml |
| Memory limited to ~2200 chars/entry | Institutional memory entries must be concise | Summarize decisions, don't dump documents |
| MCP sampling defaults: 10 RPM, 30s timeout, 4096 tokens | Too restrictive for complex agent workflows | Override in `config.yaml` per MCP server |
| Context window per model | Small models (16K) severely limit `file_read_max_chars` | Configure explicitly; Claude 200K+ is fine |
| No per-task model override in `delegate_task` | Not an issue — Abduh uses context-loading, not delegation | N/A |

## Production gotchas

1. **Profile without worker** — Tasks queue indefinitely in READY if no worker is online for that profile. Check `abduh-asof gateway status`.
2. **Workspace permissions** — Profile needs read+write on its directory. `chmod -R u+rw ~/.hermes/profiles/abduh-asof/`.
3. **Stale SOUL.md** — After updating `core/abduh-soul.md` in the repo, recopy: `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md`. Changes take effect next session (no gateway restart needed).
4. **Telegram token** — `.env` must have `TELEGRAM_BOT_TOKEN` and `TELEGRAM_ALLOWED_USERS`. Verify with `abduh-asof doctor`.
5. **NGINX timeouts** — For long generations behind a reverse proxy, set `proxy_read_timeout 600s;` and `proxy_send_timeout 600s;`.
6. **Model names are exact** — Case-sensitive, must match the provider registry exactly.
7. **Memory not persisting** — Check `~/.hermes/profiles/abduh-asof/memories/` exists and has correct permissions. Memory is injected into the system prompt at session start, not read mid-session.

## Agent output contract (AGENT-CONTRACT.md)

Every specialist agent must end its analysis with:

```
Nível de confiança: [alto | médio | baixo]
Requer validação humana: [sim | não]
Riscos identificados: [list or "nenhum"]
```

- `baixo` → Abduh does NOT deliver without requesting more context from user
- `sim` (validação humana) → response gets `⚠️ Requer validação antes de uso`
- Risks → included as a block before the main output

### Agent versioning

Every agent file must have frontmatter:

```yaml
---
agent: asof-[name]
version: 1.0.0
last_updated: YYYY-MM-DD
---
```

- Text fix → PATCH
- New rule or output field → MINOR
- Scope change → MAJOR (requires router update and new eval cycle)

## Phases

| Phase | Status | Goal |
|---|---|---|
| 1 — Foundation | **Current** | Validate institutional behavior via Claude Project (no infra yet) |
| 2 — Hermes Runtime | Planned | Migrate to Hermes-Agent with persistent memory + Telegram gateway |
| 3 — OpenClaw | Optional | Add technical/devops arm, isolated from institutional layer |
| 4 — Intranet ASOF | Future | Web interface for staff and board |

## Essential commands

```bash
# Profile management
hermes profile create abduh-asof     # Create the profile
abduh-asof setup                      # Configure API keys and model
abduh-asof doctor                     # Diagnose issues

# Chat and gateway
abduh-asof chat --verbose             # Test via CLI
abduh-asof gateway start              # Start Telegram gateway
abduh-asof gateway status             # Check gateway status

# Logs
abduh-asof logs -f                    # Follow agent log
abduh-asof logs gateway -n 100        # Last 100 gateway lines
abduh-asof logs --level WARNING --since 1h  # Filtered logs

# Memory
memory("add", "key decision or fact")    # Add to MEMORY.md
memory("replace", "old text", "new text") # Update entry
memory("remove", "outdated entry")        # Remove entry

# Skills
abduh-asof skills list                 # List installed skills
abduh-asof skills install <url>        # Install a skill
abduh-asof skills check                # Check for updates

# Deploy SOUL.md
cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md
```

## Delegation config (config.yaml)

```yaml
delegation:
  orchestrator_enabled: true       # Required for Abduh to use memory/send_message
  max_concurrent_children: 3
  max_spawn_depth: 2
  child_timeout_seconds: 300
  subagent_auto_approve: false     # Manual approval by default
  inherit_mcp_toolsets: true
  max_iterations: 50
```
