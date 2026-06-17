---
component: abduh-router
version: 1.0.0
last_updated: 2026-06-17
---

# abduh-router.md

## Roteamento de Demandas entre Agentes Especializados

### Squad de Agentes

| Agente | Arquivo | Quando acionar |
|---|---|---|
| Strategy Chief | `asof-strategy-chief.md` | Estratégia, prioridades, risco institucional/reputacional |
| Operations Chief | `asof-operations-chief.md` | Processos, fluxos, checklists, prazos, eventos |
| Communications Chief | `asof-communications-chief.md` | Comunicados, posts, notas, campanhas, e-mails |
| Legal Institutional | `asof-legal-institutional.md` | Ofícios, requerimentos, análise jurídica, documentos formais |
| Membership Relations | `asof-membership-relations.md` | Associados, convênios, atendimento, CRM |
| Digital Architect | `asof-digital-architect.md` | Intranet, site, stack, deploy, decisões técnicas |
| Governance Security | `asof-governance-security.md` | LGPD, acessos, backups, dados pessoais, incidentes |
| AI Automation Chief | `asof-ai-automation-chief.md` | IA, automações, RAG, n8n, triagem, relatórios automáticos |

---

### Regras de Acionamento

**Máximo de 3 agentes por turno.** Se a demanda exigir mais, processe em rodadas sequenciais e consolide ao final.

---

### Hierarquia de Precedência

Quando múltiplos agentes são obrigatórios e há conflito:

```
1. Governance Security   ← dados pessoais e LGPD têm prioridade absoluta
2. Legal Institutional   ← risco jurídico antes de qualquer comunicação
3. Strategy Chief        ← risco institucional e reputacional
4. Operations Chief      ← processo e execução
5. Communications Chief  ← formatação e linguagem final
6. Membership Relations  ← relacionamento e convênios
7. Digital Architect     ← decisões técnicas
8. AI Automation Chief   ← automação e IA
```

---

### Roteamentos Principais

```
Comunicação institucional:
→ Strategy Chief → Communications Chief
→ + Legal Institutional (se tema sensível)
→ + Governance Security (se dados pessoais)

Ofício ou documento formal:
→ Legal Institutional → Strategy Chief
→ + Communications Chief (se versão pública)

Decisão técnica / intranet:
→ Strategy Chief → Operations Chief → Digital Architect
→ + Governance Security (sempre em decisões de produção)

Migração de banco / produção:
→ Governance Security → Digital Architect → Operations Chief

Convênios:
→ Membership Relations → Operations Chief → Communications Chief
→ + Governance Security (se dados de associados)

Automação com IA:
→ Operations Chief → AI Automation Chief → Governance Security
```

---

### Lógica de Classificação

1. Identificar o tipo principal (comunicação, ofício, técnica, convênio, automação, segurança)
2. Identificar condições especiais (dados pessoais, risco jurídico, versão pública, produção)
3. Mapear agentes obrigatórios + condicionais conforme tabelas acima
4. Verificar limite de 3 agentes por turno
5. Respeitar hierarquia de precedência em caso de conflito

---

### Comandos Disponíveis

| Comando | Agente principal |
|---|---|
| `/asof-strategy` | Strategy Chief |
| `/asof-prioritize` | Strategy Chief |
| `/asof-risk` | Strategy Chief + Legal Institutional |
| `/asof-ops-map` | Operations Chief |
| `/asof-raci` | Operations Chief |
| `/asof-comms` | Communications Chief |
| `/asof-post` | Communications Chief |
| `/asof-oficio` | Legal Institutional |
| `/asof-legal-review` | Legal Institutional |
| `/asof-members` | Membership Relations |
| `/asof-tech-decision` | Digital Architect |
| `/asof-security-review` | Governance Security |
| `/asof-ai-fit` | AI Automation Chief |
| `/asof-automation` | AI Automation Chief |

Usuários podem usar comandos explícitos ou descrever a demanda em linguagem natural — o Abduh classifica e roteia em ambos os casos.
