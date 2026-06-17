<!-- Parent: ../AGENTS.md -->
<!-- Generated: 2026-06-17 | Updated: 2026-06-17 -->

# evals

## Purpose
This directory holds evaluation test cases for the Abduh institutional agent system. Each test scenario exercises the full routing and consolidation pipeline: user input is classified, up to three specialist agents are loaded as context, their analyses are consolidated by Abduh, the quality gate is applied, and a final institutional response is produced. The test suite covers the eight specialist domains, edge cases involving agent precedence conflicts, quality gate rejections, and multi-round sequential processing when more than three agents are required.

## Planned Files
| File | Description | Status |
|------|-------------|--------|
| `casos-teste.md` | 13 institutional test scenarios | Planned |

## For AI Agents

### Working In This Directory
- Before creating evaluation files, read `../AGENT-CONTRACT.md` to understand the mandatory output contract every specialist agent must satisfy.
- Read `../core/abduh-soul.md` for sovereign rules and the quality gate criteria that determine pass/fail for each test case.
- Read `../abduh-router.md` (when it exists) to understand how demands are classified and which agents are selected.
- New test files must be plain Markdown; no code execution or external API calls are performed during evaluation.
- When adding scenarios to `casos-teste.md`, assign a sequential ID (`TC-01` through `TC-13`) and keep each scenario self-contained.
- Do not create sub-directories here without updating the parent `../AGENTS.md` file tree.

### Test Case Format
Each test case in `casos-teste.md` should follow this structure:

```markdown
## TC-XX — <Short scenario title>

**Input**
> Verbatim user message (Portuguese, as it would arrive via Telegram or CLI).

**Expected agents** (max 3, in precedence order)
1. `<agent-name>` — reason for inclusion
2. `<agent-name>` — reason for inclusion

**Expected output format**
- Tone: [formal | informativo | de alerta]
- Length: [curto ≤150 words | médio 150–400 | longo >400]
- Blocks required: [risk block | human-validation warning | confidence footer]

**Quality gate**
- Result: [PASS | FAIL]
- Justification: <Why the gate passes or blocks this output>

**Agent contract fields** (for each agent invoked)
- Nível de confiança: [alto | médio | baixo]
- Requer validação humana: [sim | não]
- Riscos identificados: [list or "nenhum"]

**Notes**
<Optional: edge cases, precedence conflicts, multi-round processing notes>
```

<!-- MANUAL: Add new test scenario IDs here as they are created. Maintain coverage across all 8 specialist domains. Flag any scenario that exercises a sovereign rule override with the label [SOVEREIGN]. -->
