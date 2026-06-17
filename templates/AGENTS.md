<!-- Parent: ../AGENTS.md -->
<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# templates

## Purpose

This directory contains the four official institutional document templates used by Abduh to generate ASOF correspondence and publications. All templates follow the Manual de Redação da Presidência da República and ASOF internal standards. They are the authoritative source for structure, fillable fields, style rules, and quality gate checklists for each document type. AI agents must use these templates when generating or reviewing institutional documents — never invent a structure from scratch.

## Key Files

| File | Description |
|------|-------------|
| `oficio.md` | Official correspondence template (Ofício) for formal external communications with government bodies, partner institutions, and authorities |
| `nota-institucional.md` | Public institutional statement template (Nota Pública) for positions, clarifications, and announcements issued under the ASOF name |
| `comunicado-associados.md` | Member communication template (Comunicado/Circular) for announcements, notices, and calls-to-action directed at ASOF associados |
| `relatorio-diretoria.md` | Internal board report template (Relatório de Diretoria) for structured reporting on activities, decisions, and recommendations to the directorate |

## For AI Agents

### Working In This Directory

- Always load the relevant template before generating or reviewing any institutional document. Do not produce a document without consulting the corresponding template.
- Every fillable field listed in the template's fields table is mandatory. Never invent values for number, date, recipient, or reference fields — request the missing data from the user or flag the gap.
- The quality gate checklist at the end of each template must be evaluated before marking a document ready. If any checklist item fails, the document must not be delivered without flagging the failure.
- LGPD compliance is a hard constraint for all four templates: no unnecessary personal data of associados may appear in the document body.
- Documents generated from these templates always require human validation before external use. Mark all drafts with `⚠️ Requer validação antes de uso`.
- When updating a template file (not a document generated from it), increment the version in the frontmatter following the agent versioning scheme: PATCH for text fixes, MINOR for new rule or output field, MAJOR for scope change (which also requires router update and a new eval cycle).

### Template Selection Guide

| Demand type | Template to use |
|---|---|
| Formal correspondence to a government body, authority, or external institution | `oficio.md` |
| Public statement, position, or clarification to be published under the ASOF name | `nota-institucional.md` |
| Announcement, notice, or call-to-action directed at ASOF members | `comunicado-associados.md` |
| Structured internal report on activities, decisions, or recommendations for the directorate | `relatorio-diretoria.md` |

If the demand could match more than one template, prefer the most formal option. When in doubt between `oficio.md` and `nota-institucional.md`, check the audience: external authority → ofício; general public or press → nota institucional.

### Style Rules

**General rules (all documents)**
- Language: formal Brazilian Portuguese per the Manual de Redação da Presidência da República.
- No emojis, advertising language, or informal expressions in any template output.
- First mention of ASOF must spell out the full name: "Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro (ASOF)".
- Dates follow the Brazilian standard: `DD de [month written out] de YYYY`.
- LGPD hard rule: do not include sensitive personal data of associados in the body of any document.

**Ofício-specific rules**
- Body paragraphs must be numbered sequentially: 1., 2., 3.
- No comma after the vocativo (per Manual de Redação da PR).
- Closing: use "Atenciosamente" for peers; use "Respeitosamente" only for superior authorities (Minister, President).
- Vocativo must match the gender of the addressee.
- The ofício number and date must be real values — never invented placeholders.

**Nota Institucional-specific rules**
- Requires Strategy Chief review before publication due to reputational risk.
- Structure body as three logical blocks: fato / posição institucional / encaminhamento.
- Always include press contact block at the closing.
- Always requires human validation — notas are public and irreversible.

**Comunicado aos Associados-specific rules**
- Tone: cordial but institutional. Not informal, not cold.
- Structure body as: contexto / informação / ação esperada.
- Must be reviewed by Membership Relations before sending.
- If published externally, also requires Communications Chief review.
- No sensitive personal data in mass communications.

**Relatório de Diretoria-specific rules**
- Internal use only. Objective language with no rhetoric or persuasive framing.
- Executive summary must be 3 to 5 lines.
- Topic sections must be numbered.
- Flag explicitly if the report contains sensitive data requiring access restriction.
- Flag explicitly if formal board approval is required for any recommendation.
- All annexes must be listed with a clear description.

## Dependencies

### Internal

- `../core/abduh-soul.md` — Sovereign rules and identity constraints that override all template defaults. Any conflict between a template rule and the SOUL resolves in favor of the SOUL.
- `../AGENT-CONTRACT.md` — Mandatory output contract. Any agent analysis that informs a document generated from these templates must end with the three required contract fields (confidence level, human validation flag, identified risks).
- `../agents/` — Specialist agents whose review is required before certain templates are finalized: Governance Security and Legal Institutional for ofícios with legal exposure; Strategy Chief for notas institucionais; Membership Relations for comunicados; Operations Chief for relatórios.
- `../PRD-abduh-asof.md` — Full product spec and ADRs. Consult for any structural decision about template scope or institutional standards.

<!-- MANUAL: Add cross-references to evals/ once evaluation cases for template rendering are created in Phase 1. -->
