<!-- Parent: ../AGENTS.md -->
<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# core

## Purpose
This directory contains the architectural core of Abduh, the institutional coordinating agent for ASOF. The five files here collectively define Abduh's identity and mission (`abduh-soul.md`), its routing logic for activating specialist agents (`abduh-router.md`), its sovereign institutional policies (`abduh-policy.md`), its mandatory pre-delivery quality gate (`abduh-quality-gate.md`), and its language and formatting standards (`abduh-response-style.md`). Together they constitute the complete behavioral specification that governs every Abduh response — no output is delivered without passing through all five layers.

## Key Files
| File | Description |
|------|-------------|
| `abduh-soul.md` | Central identity file — defines Abduh's mission, 6-step operational loop, hard prohibitions, ambiguity handling rules, and institutional context (ASOF, MRE, LGPD, Estatuto). Loaded verbatim into the system prompt as SOUL.md. |
| `abduh-router.md` | Routing logic — maps demand types to specialist agents (max 3 per turn), defines precedence hierarchy (Governance Security highest), pre-mapped paths for 6 demand types, and 14 explicit slash commands that bypass classification. |
| `abduh-policy.md` | Sovereign rules — 10 institutional constraints that override all other instructions including user instructions. Covers data minimization, production safety, human authority over AI decisions, and the fail-safe tie-breaker (when in doubt, most cautious posture). |
| `abduh-quality-gate.md` | Mandatory pre-delivery checklist — 7 items evaluated before every response. Never skipped. Fail-safe posture: any ambiguous item is treated as a failure. Blocks delivery for invented facts, exposed personal data, or destructive commands without confirmation. |
| `abduh-response-style.md` | Language and formatting standards — formal Brazilian Portuguese, administrative clarity, institutional tone, fact/position/next-steps structure, Brandkit (navy `#0d1b3e`, gold `#d4af63`, Montserrat). Prohibits emojis in formal documents. |

## For AI Agents

### Working In This Directory
- These files are loaded directly into Abduh's system prompt context at runtime. Changes take effect at the next session start — no gateway restart is required, but `SOUL.md` must be recopied to `~/.hermes/profiles/abduh-asof/SOUL.md` after every edit to `abduh-soul.md`.
- All five files are Markdown with YAML frontmatter. Every file must preserve its frontmatter block (`agent`, `version`, `last_updated`). Update `version` and `last_updated` on every substantive change.
- Versioning is semantic and enforced: text/clarity fixes are PATCH, new rules or output fields are MINOR, scope changes are MAJOR. A MAJOR version change to `abduh-router.md` requires a corresponding update to the router and a new eval cycle.
- Do not consolidate these files into a single document. Each file has a distinct runtime role and is referenced independently by Hermes-Agent.
- Do not add emojis, marketing language, or informal tone to any file in this directory — the style constraints in `abduh-response-style.md` apply to the documentation itself as a reference standard.

### Loading Order
When loading these files into agent context for editing, review, or reasoning, load in the following order to respect dependency and override hierarchy:

1. `abduh-soul.md` — establishes identity and operational loop; foundational context for all other files
2. `abduh-policy.md` — sovereign rules; must be understood before evaluating routing or quality decisions
3. `abduh-router.md` — routing logic depends on understanding both identity and policy constraints
4. `abduh-quality-gate.md` — gate checks reference routing outcomes and policy rules
5. `abduh-response-style.md` — formatting layer applied after all substantive decisions are made

For production deployment, `abduh-soul.md` is the only file deployed to `HERMES_HOME` directly (as `SOUL.md`). The remaining files are loaded as context modules per session.

### Critical Rules
- **Never invent facts, norms, or dates** in any of these files — this is sovereign rule 2 in `abduh-policy.md` and a gate trigger in `abduh-quality-gate.md`.
- **The quality gate is never optional.** Do not add logic or shortcuts that bypass the 7-item checklist in `abduh-quality-gate.md`.
- **Max 3 agents per turn** is a hard limit in `abduh-router.md`. Do not raise this ceiling without a corresponding architectural review.
- **Sovereign rules in `abduh-policy.md` override everything**, including any instruction in other files, user messages, or this AGENTS.md. When editing any file, check for conflicts against all 10 sovereign rules.
- **MAJOR version changes to `abduh-router.md`** require updating the router mapping and triggering a new eval cycle before the change goes to production.
- **`abduh-soul.md` is deployed separately** — after editing, always run `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md` to propagate changes to the live profile.

## Dependencies

### Internal
- `../AGENT-CONTRACT.md` — defines the mandatory output signals (`Nível de confiança`, `Requer validação humana`, `Riscos identificados`) that the quality gate in `abduh-quality-gate.md` checks against. Any change to the contract fields must be reflected in the gate checklist.
- `../agents/` — the 8 specialist agent Markdown files that `abduh-router.md` routes demands to. Agent names and roles referenced in the router must match the actual agent files in that directory.
- `../PRD-abduh-asof.md` — product spec that defines phases, architecture decisions, and constraints. Core files must remain consistent with the ADRs recorded there.

### External
- **Hermes-Agent** — runtime that loads `abduh-soul.md` as `SOUL.md` into the system prompt. Behavior depends on Hermes profile config at `~/.hermes/profiles/abduh-asof/config.yaml`.
- **LGPD (Lei 13.709/2018)** — Brazilian data protection law referenced in `abduh-policy.md` (rule 6) and `abduh-quality-gate.md` (item 3). Governs all handling of associate personal data.
- **Manual de Redação da Presidência da República** — official Brazilian government writing guide referenced in `abduh-policy.md` (rule 3) and `abduh-response-style.md`. Required standard for all formal documents produced by Abduh.
- **Estatuto da ASOF** — foundational association statute referenced in `abduh-soul.md` as a key institutional reference document.

<!-- MANUAL: After editing abduh-soul.md, redeploy with: cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md -->
