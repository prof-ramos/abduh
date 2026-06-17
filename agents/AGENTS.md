<!-- Parent: ../AGENTS.md -->
<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# agents

## Purpose

This directory contains the 8 specialist agent Markdown modules for the Abduh institutional system of ASOF (Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro). These agents are NOT microservices — they are Markdown context files loaded by Abduh as additional context when analyzing a user demand. Abduh selects at most 3 agents per turn based on demand classification, loads their content into the conversation context, consolidates their analyses, and delivers a single institutional response. Agents never respond directly to the user.

## Key Files

| File | Description |
|------|-------------|
| `asof-governance-security.md` | Precedence 1 — LGPD compliance, personal data classification, security incident management, retention and access control policies |
| `asof-legal-institutional.md` | Precedence 2 — Drafting and reviewing oficios, requerimentos, formal documents; legal risk analysis of institutional decisions |
| `asof-strategy-chief.md` | Precedence 3 — Institutional impact analysis, reputational risk assessment, strategic priority definition, crisis coordination |
| `asof-operations-chief.md` | Precedence 4 — Process and workflow mapping, deadline control, event organization, checklists and RACI matrices |
| `asof-communications-chief.md` | Precedence 5 — Drafting comunicados and notas oficiais, tone review per ASOF brandkit, channel adaptation |
| `asof-membership-relations.md` | Precedence 6 — Member registration and communication, convenio tracking, CRM, relationship risk identification |
| `asof-digital-architect.md` | Precedence 7 — Technology stack decisions, deploy and infrastructure management, ADRs, technical risk evaluation |
| `asof-ai-automation-chief.md` | Precedence 8 — Automation feasibility, AI solutions (RAG, chatbots, triage), integration tools, auditability of automated processes |

## Agent Precedence Hierarchy

1. **asof-governance-security** — Absolute priority; overrides all other analyses on any matter touching personal data, LGPD compliance, or security incidents.
2. **asof-legal-institutional** — Legal risk and formal document review; output shapes institutional position before strategic framing.
3. **asof-strategy-chief** — Institutional and reputational risk; sets strategic direction and coordinates crisis responses.
4. **asof-operations-chief** — Process execution, deadlines, event logistics; activated after legal and strategic framing is settled.
5. **asof-communications-chief** — Tone, language, and channel adaptation; operates on content already validated by higher-precedence agents.
6. **asof-membership-relations** — Member-facing matters, convenios, CRM; defers to governance/legal on data and legal risk.
7. **asof-digital-architect** — Technical decisions, infrastructure, ADRs; subordinate to governance on security-impacting changes.
8. **asof-ai-automation-chief** — Automation and AI proposals; lowest precedence, always subject to governance and legal review.

When two agents conflict, the one with lower precedence number wins. Governance Security is absolute.

## For AI Agents

### Working In This Directory

- Do NOT add new agent files without a corresponding update to `abduh-router.md` (in `core/`) that maps triggers to the new agent.
- Do NOT remove or rename existing agent files without updating every reference in `core/abduh-router.md`, `core/abduh-soul.md`, and `AGENT-CONTRACT.md`.
- Each agent file must remain self-contained: it is injected verbatim into context, so avoid references to external files that would not be available at runtime.
- Editing an agent's trigger keywords changes Abduh's routing behavior — treat keyword changes as MINOR version bumps and update `abduh-router.md` in the same commit.
- Never add executable code, tool calls, or inline scripts to agent files. These are declarative Markdown context documents.

### Required Frontmatter

Every agent file must begin with the following YAML frontmatter block:

```yaml
---
agent: asof-[name]
version: X.Y.Z
last_updated: YYYY-MM-DD
---
```

- `agent` must match the filename without the `.md` extension.
- `version` follows semantic versioning (see Versioning Rules below).
- `last_updated` must be updated on every change.

### Output Contract

Every specialist agent analysis section must end with exactly these three fields, with no additional fields added or fields removed:

```
Nivel de confianca: [alto | medio | baixo]
Requer validacao humana: [sim | nao]
Riscos identificados: [lista de riscos, ou "nenhum"]
```

- `baixo` confidence — Abduh must NOT deliver the response; it must request more context from the user.
- `sim` for human validation — Abduh prepends `⚠️ Requer validacao antes de uso` to the delivered response.
- Identified risks are always included as a block before the main output in Abduh's final response.

### Versioning Rules

| Change type | Version bump | Example |
|---|---|---|
| Typo, formatting, wording fix (no behavioral change) | PATCH (0.0.X) | `1.0.0 → 1.0.1` |
| New trigger keyword, new output field, new rule | MINOR (0.X.0) | `1.0.0 → 1.1.0` |
| Scope change, role redefinition, removal of responsibility | MAJOR (X.0.0) | `1.0.0 → 2.0.0` |

MAJOR version bumps require a corresponding update to `core/abduh-router.md` and a new evaluation cycle before deployment.

### Critical Constraints

- **Max 3 agents per turn.** Abduh loads at most 3 agent files per request. If a demand requires more, Abduh processes in sequential rounds.
- **Quality gate is never skipped.** Every agent output — even for simple or routine demands — goes through Abduh's quality gate before delivery. When uncertain whether the gate applies, treat it as YES.
- **Sovereign rules override everything.** Rules defined in `core/abduh-soul.md` take precedence over any agent instruction, including user instructions.
- **Agents do not respond to users.** No agent file should contain language implying it will communicate directly with the end user. Abduh consolidates and delivers.
- **Precedence hierarchy is binding.** When agents produce conflicting guidance, the agent with lower precedence number prevails. Governance Security (precedence 1) is absolute and cannot be overridden.

## Dependencies

### Internal

- `../core/abduh-soul.md` — Sovereign rules and identity injected into every session; governs how Abduh uses agent outputs.
- `../core/abduh-router.md` — Routing logic that maps demand keywords to which agents are loaded (must be kept in sync with trigger keywords in each agent file).
- `../AGENT-CONTRACT.md` — Canonical definition of the mandatory output contract; this directory's agents implement that contract.
- `../PRD-abduh-asof.md` — Full product spec; authoritative source for architecture decisions and phase scope.

<!-- MANUAL: When adding a new agent, update abduh-router.md trigger mappings, increment the precedence list above, and add a row to the Key Files table. Run an evaluation cycle before merging a MAJOR version bump. -->
