# PRD — Projeto Abduh

## ASOF Executive Operating Squad

**Versão:** 0.3.0
**Data:** 2026-06-16
**Autor:** Prof. Ramos
**Status:** Draft
**Audiência:** Arquiteto/desenvolvedor + Claude Code (Context7 MCP)

**Changelog:**

- `0.3.0` — Seção 3.2 corrigida com estrutura real do Hermes-Agent (Context7); ADR-004 adicionado (ponto único de entrada); ADR-005 limpo (apenas contrato de interface); docs/dev-guide.md adicionado ao repo; Fase 2 entregável corrigido (SOUL.md copiado, não traduzido)
- `0.2.1` — Removida tabela de estimativa de tokens (prematura); AGENT-CONTRACT simplificado de especificação formal para convenção leve; formalização YAML adiada para Fase 2
- `0.2.0` — Adicionadas seções 4.3 (gestão de contexto), 4.4 (precedência de agentes); quality gate expandido com ações por item; critério de promoção de fase; ADR-005

---

## 1. Visão Geral

### 1.1 Problema

A ASOF (Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro) opera com equipe reduzida e demandas administrativas, jurídico-institucionais e comunicacionais complexas. Não existe hoje nenhum sistema de apoio à decisão, roteamento de tarefas ou memória institucional — cada demanda começa do zero, sem contexto acumulado, sem cautela automatizada, sem padronização de linguagem ou risco.

### 1.2 Solução

**Abduh** é o agente coordenador institucional permanente da ASOF. Ele atua como ponto único de entrada para funcionários e diretoria, orquestrando internamente uma squad de 8 agentes especializados, aplicando regras institucionais, preservando memória operacional e entregando resposta final com voz única e tom cauteloso.

Abduh **não é** um chatbot genérico. É um assistente institucional persistente, com identidade, memória, skills, rotinas e quality gate próprios.

### 1.3 Nome técnico vs. institucional

| Contexto | Nome |
|---|---|
| Interno / técnico | Abduh |
| Interface para funcionários/diretoria | Assistente ASOF |

---

## 2. Objetivos

### 2.1 Objetivos primários

- Centralizar demandas administrativas, jurídicas, comunicacionais e técnicas da ASOF em um único ponto de entrada
- Preservar memória institucional entre sessões
- Garantir tom, cautela jurídica e padrão ASOF em toda saída
- Rotear demandas para os agentes especializados corretos sem intervenção manual do usuário
- Bloquear respostas inseguras, incompletas ou arriscadas antes de entregá-las

### 2.2 Objetivos secundários

- Preparar infraestrutura para integração futura com intranet ASOF
- Criar base evolutiva de skills ASOF versionadas em GitHub
- Estabelecer separação clara entre camada institucional (Hermes) e camada técnica/devops (OpenClaw)

### 2.3 Não-objetivos (fora do escopo v1)

- Interface web ou intranet (fase futura)
- Multi-usuário com RBAC completo (fase futura)
- Integração com base de dados de associados (fase futura)
- OpenClaw como camada ativa (opcional, pós-validação)
- Fine-tuning ou treinamento de modelo

---

## 3. Stack Tecnológica

### 3.1 Runtime principal

| Componente | Tecnologia | Justificativa |
|---|---|---|
| Agent runtime | Hermes-Agent (NousResearch) | Memória persistente, SOUL.md, skills evolutivas, cron, VPS-ready |
| LLM backend | Claude (Anthropic API) via proxy OpenAI-compatible | Model-agnostic do Hermes; Claude é o padrão institucional |
| Gateway de mensagens | Telegram (fase 1) | Canal mais simples para validação; Discord/Slack em fases futuras |
| Infraestrutura | VPS própria + Docker + Traefik + Portainer | Alinhado com stack existente do proj. Ramos |
| Versionamento | GitHub — repositório `asof-agent-squad` | Fonte da verdade de todos os agentes |
| Executor técnico | OpenClaw (opcional, fase 2+) | Tarefas devops, código, VPS, isolado da camada institucional |

### 3.2 Estrutura de arquivos Hermes

Perfil `abduh-asof` criado via `hermes profile create abduh-asof`. Cada perfil é um `HERMES_HOME` totalmente independente com config, memória, skills e gateway próprios. Uso: `hermes -p abduh-asof gateway start`.

```
~/.hermes/profiles/abduh-asof/
├── .env                    # API keys, bot tokens (chmod 600)
├── config.yaml             # Modelo, provider, toolsets, gateway, delegação
├── auth.json               # Credenciais OAuth (Nous Portal, etc.)
├── SOUL.md                 # Identidade, regras soberanas, tom
├── memories/               # Memória persistente (MEMORY.md, USER.md)
├── skills/                 # Skills ASOF versionadas
├── cron/                   # Rotinas recorrentes
├── sessions/               # Sessões do gateway (JSONL + snapshots)
├── state.db                # Store canônico de sessões (SQLite + FTS5)
└── logs/                   # Logs (errors.log, gateway.log — secrets redacted)
```

O profile default (`~/.hermes/`) é backward-compatible. Para o Abduh, usamos sempre o perfil nomeado `abduh-asof`, que isola completamente state, memória, skills e gateway de outros perfis.

### 3.3 Repositório GitHub

```
asof-agent-squad/
├── README.md
├── AGENTS.md                     # Índice e descrição de todos os agentes
├── CHANGELOG.md
├── PRD.md                        # Este documento
├── core/
│   ├── abduh-soul.md             # SOUL.md principal do Abduh
│   ├── abduh-router.md           # Lógica de roteamento entre agentes
│   ├── abduh-policy.md           # Regras soberanas institucionais
│   ├── abduh-response-style.md   # Tom, linguagem, formato de saída
│   └── abduh-quality-gate.md     # Checklist pré-entrega
├── agents/
│   ├── AGENT-CONTRACT.md             # Contrato de interface obrigatório para todos os agentes
│   ├── asof-strategy-chief.md
│   ├── asof-operations-chief.md
│   ├── asof-communications-chief.md
│   ├── asof-legal-institutional.md
│   ├── asof-membership-relations.md
│   ├── asof-digital-architect.md
│   ├── asof-governance-security.md
│   └── asof-ai-automation-chief.md
├── templates/
│   ├── oficio.md
│   ├── nota-institucional.md
│   ├── comunicado-associados.md
│   └── relatorio-diretoria.md
├── evals/
│   ├── casos-teste.md
│   └── resultados/
└── docs/
    ├── dev-guide.md                  # Guia do desenvolvedor
    ├── decisoes-arquiteturais/   # ADRs
    └── onboarding.md
```

---

## 4. Arquitetura do Sistema

### 4.1 Fluxo principal

```
Usuário (Telegram / CLI)
↓
Hermes Gateway
↓
Abduh — SOUL.md + abduh-router.md
↓
Classificação de demanda
↓
Consulta aos agentes internos necessários (Markdown em contexto)
↓
Consolidação das análises
↓
abduh-quality-gate.md
↓
Resposta final institucional
```

### 4.2 Modelo mental dos agentes

Os 8 agentes especializados **não respondem diretamente ao usuário**. Eles funcionam como módulos de análise internos — arquivos Markdown carregados no contexto do Abduh conforme o tipo de demanda. O Abduh consolida, filtra e entrega a resposta final com voz única.

### 4.3 Gestão de contexto

Para preservar qualidade de raciocínio e evitar degradação silenciosa por sobrecarga de contexto, o Abduh segue estas regras:

- **Máximo de 3 agentes ativos por turno.** Se a demanda exigir mais, o Abduh processa em rodadas sequenciais e consolida ao final.
- **Carregamento seletivo.** Apenas os agentes mapeados pelo `abduh-router.md` para aquela demanda específica são carregados. Nunca todos os 8 simultaneamente.
- **Prioridade de carregamento em demandas pesadas** (ex: revisão de documento longo + múltiplos agentes): carregar primeiro os agentes obrigatórios, depois os condicionais se o contexto permitir.
- **Limite prático:** ajustar com base no uso real na Fase 1 — os números concretos emergem da observação, não da estimativa prévia.

### 4.4 Precedência de agentes em conflito

Quando múltiplos agentes são obrigatórios simultaneamente e há conflito de orientação, a hierarquia de precedência é:

```
1. Governance Security   ← LGPD e dados pessoais sempre têm prioridade absoluta
2. Legal Institutional   ← Risco jurídico antes de qualquer comunicação
3. Strategy Chief        ← Risco institucional/reputacional
4. Operations Chief      ← Processo e execução
5. Communications Chief  ← Formatação e linguagem final
6. Membership Relations  ← Relacionamento e convênios
7. Digital Architect     ← Decisões técnicas
8. AI Automation Chief   ← Automação e IA
```

**Regra de desempate:** em caso de conflito entre orientações de dois agentes de mesma camada, o Abduh sinaliza o conflito explicitamente na resposta e indica qual orientação adotou e por quê. Nunca resolve conflito de forma silenciosa.

### 4.5 Roteamentos principais

**Comunicação institucional**

```
Strategy Chief → Communications Chief → [Legal Institutional, se tema sensível] → [Governance Security, se dados pessoais]
```

**Ofício ou documento formal**

```
Legal Institutional → Strategy Chief → [Communications Chief, se versão pública]
```

**Intranet / decisão técnica**

```
Strategy Chief → Operations Chief → Digital Architect → Governance Security → [AI Automation Chief, se IA]
```

**Migração de banco / produção**

```
Governance Security → Digital Architect → Operations Chief
```

> Regra: segurança antes de implementação, sempre.

**Convênios**

```
Membership Relations → Operations Chief → Communications Chief → [Governance Security, se dados de associados]
```

**Automação com IA**

```
Operations Chief → AI Automation Chief → Governance Security → Digital Architect
```

---

## 5. Componentes do Abduh

### 5.1 `abduh-soul.md` — Identidade institucional

Define:

- Quem é o Abduh (assistente institucional permanente da ASOF)
- Missão e escopo
- Regras soberanas (ver seção 6)
- Limites de permissão por perfil de usuário
- Comportamento em situações ambíguas ou de risco

### 5.2 `abduh-router.md` — Roteamento

Define qual agente acionar por tipo de demanda, respeitando a hierarquia de precedência (seção 4.4) e o limite de contexto (seção 4.3). Todo agente acionado deve retornar saída conforme o contrato definido em `agents/AGENT-CONTRACT.md`.

Exemplo de lógica de roteamento:

```
SE demanda envolver ofício, requerimento, resposta formal ao MRE:
→ OBRIGATÓRIO: asof-legal-institutional
→ SE risco institucional: + asof-strategy-chief
→ SE versão pública: + asof-communications-chief

SE demanda envolver dados de associados, LGPD, acessos:
→ OBRIGATÓRIO: asof-governance-security
→ SE automação: + asof-ai-automation-chief

SE demanda envolver post, carrossel, comunicado, nota:
→ OBRIGATÓRIO: asof-communications-chief
→ SE tema sensível: + asof-legal-institutional
→ SE risco reputacional: + asof-strategy-chief
```

### 5.3 `abduh-policy.md` — Regras soberanas

1. A ASOF é entidade representativa dos Oficiais de Chancelaria do Serviço Exterior Brasileiro
2. O Abduh não inventa fatos, números, normas, nomes, datas ou deliberações
3. Quando houver dúvida factual, sinaliza necessidade de conferência documental
4. Documentos oficiais seguem Manual de Redação da Presidência da República
5. Comunicações públicas consideram risco reputacional antes de qualquer saída
6. Demandas jurídicas separam fato, fundamento, interpretação e pedido
7. Dados de associados são tratados com cautela, minimização e controle de acesso
8. Nenhuma ação em produção ocorre sem backup, rollback e autorização explícita
9. IA é apoio, não autoridade final em decisões sensíveis
10. Comandos destrutivos exigem confirmação humana antes de execução

### 5.4 `abduh-response-style.md` — Tom e linguagem

- Português-BR formal
- Clareza administrativa — frases diretas, sem retórica
- Tom institucional — sem linguagem publicitária ou promocional
- Sem informalidade excessiva
- Sem exagero retórico
- Separar sempre: fato / posição institucional / encaminhamento
- Sinalizar explicitamente pontos que exigem validação humana

### 5.5 `abduh-quality-gate.md` — Checklist pré-entrega

Antes de qualquer resposta, verificar cada item e executar a ação correspondente:

```
[ ] A resposta inventou algum fato ou norma?
    → SIM: descartar saída; reprocessar com instrução explícita de não-invenção;
            indicar na resposta que conferência documental é necessária

[ ] Citou norma sem base documental verificável?
    → SIM: remover a citação; substituir por "recomenda-se verificar a norma aplicável"

[ ] Há dado pessoal de associado exposto indevidamente?
    → SIM: bloquear entrega; não registrar em log externo;
            notificar operador antes de qualquer outro passo

[ ] Há risco reputacional não sinalizado?
    → SIM: adicionar bloco de aviso explícito antes da saída;
            acionar asof-strategy-chief se ainda não estava no roteamento

[ ] A linguagem está institucional e dentro do tom ASOF?
    → NÃO: reprocessar via abduh-response-style.md antes de entregar

[ ] A saída está pronta para uso direto sem edição?
    → NÃO: sinalizar quais partes precisam de revisão humana antes do uso

[ ] Há pontos que exigem validação humana?
    → SIM: entregar com marcação explícita "⚠️ Requer validação antes de uso"
            listando os pontos específicos que precisam de revisão

[ ] O comando aciona ação destrutiva em produção sem confirmação?
    → SIM: bloquear execução; solicitar confirmação explícita do operador;
            nunca executar automaticamente
```

**Regra geral:** o quality gate nunca é ignorado, mesmo para demandas simples. Em caso de dúvida sobre qualquer item, tratar como SIM.

---

## 6. Squad de Agentes Especializados

### 6.1 Índice

| Agente | Arquivo | Função central |
|---|---|---|
| Strategy Chief | `asof-strategy-chief.md` | Estratégia institucional, prioridades, riscos |
| Operations Chief | `asof-operations-chief.md` | Processos, fluxos, checklists, prazos |
| Communications Chief | `asof-communications-chief.md` | Comunicação institucional, redes, notas |
| Legal Institutional | `asof-legal-institutional.md` | Ofícios, requerimentos, análise jurídica |
| Membership Relations | `asof-membership-relations.md` | Associados, convênios, CRM, atendimento |
| Digital Architect | `asof-digital-architect.md` | Intranet, site, stack, deploy, ADRs |
| Governance Security | `asof-governance-security.md` | LGPD, acessos, backups, dados, incidentes |
| AI Automation Chief | `asof-ai-automation-chief.md` | IA, automações, RAG, n8n, triagem |

### 6.2 Saídas padrão por agente

**Strategy Chief**

```
1. Diagnóstico institucional
2. Objetivo da ASOF
3. Stakeholders envolvidos
4. Riscos (jurídico / administrativo / reputacional)
5. Recomendação
6. Próximos passos
7. Agentes a acionar
```

**Operations Chief**

```
Processo:
Objetivo:
Entrada:
Responsável:
Etapas:
Prazo:
Critério de conclusão:
Risco:
Indicador:
```

**Communications Chief**

```
Canal:
Público-alvo:
Objetivo:
Mensagem central:
Tom:
Texto sugerido:
Risco de interpretação:
Ajustes recomendados:
```

**Legal Institutional**

```
Tipo de documento:
Finalidade:
Destinatário:
Base factual:
Risco jurídico:
Risco institucional:
Texto sugerido:
Pontos que exigem validação:
```

**Governance Security — Matriz LGPD mínima**

```
Há dado pessoal?
Há dado sensível?
Quem é o controlador?
Quem é o operador?
Qual a finalidade?
Qual a base legal provável?
Há necessidade de retenção?
Quem pode acessar?
Há log?
Há backup?
Há risco de compartilhamento indevido?
```

**AI Automation Chief — Checklist pré-IA**

```
Fonte de verdade: de onde vem a informação confiável?
Risco de erro: um erro causa dano institucional?
Revisão humana: precisa de aprovação antes de enviar/publicar?
Dados pessoais: há risco LGPD?
Custo: quanto por execução?
Auditabilidade: dá para rastrear o que foi feito?
Fallback: existe caminho manual?
```

---

## 7. Comandos

### 7.1 Comandos institucionais

| Comando | Agente principal | Descrição |
|---|---|---|
| `/asof-strategy` | Strategy Chief | Estratégia institucional |
| `/asof-prioritize` | Strategy Chief | Priorização de demandas |
| `/asof-risk` | Strategy Chief + Legal | Risco jurídico, administrativo e reputacional |
| `/asof-ops-map` | Operations Chief | Mapeamento de processo |
| `/asof-raci` | Operations Chief | Matriz de responsabilidades |
| `/asof-comms` | Communications Chief | Comunicação institucional |
| `/asof-post` | Communications Chief | Post / rede social |
| `/asof-oficio` | Legal Institutional | Documento formal |
| `/asof-legal-review` | Legal Institutional | Revisão jurídico-institucional |
| `/asof-members` | Membership Relations | Associados, convênios, relacionamento |
| `/asof-tech-decision` | Digital Architect | Decisão técnica |
| `/asof-security-review` | Governance Security | LGPD, acesso, dados, produção |
| `/asof-ai-fit` | AI Automation Chief | Avaliar se IA faz sentido |
| `/asof-automation` | AI Automation Chief | Desenho de automação |

### 7.2 Comandos técnicos (OpenClaw — fase 2)

| Comando | Descrição |
|---|---|
| `/asof-code-review` | Revisão de repositório |
| `/asof-deploy-check` | Verificação pré-deploy |
| `/asof-intranet-issue` | Debug/issue de intranet |
| `/asof-vps-maintenance` | Manutenção de VPS |

---

## 8. Brandkit ASOF (referência para Communications Chief)

| Elemento | Padrão |
|---|---|
| Cor primária | `#0d1b3e` (navy) |
| Cor de acesso | `#d4af63` (gold) |
| Tipografia | Montserrat Bold (títulos), Montserrat Regular (corpo) |
| Ícones | Tabler Icons CDN |
| Estilo | Institucional, escuro, editorial, alto contraste |
| Tom | Formal, contido, claro, sem emojis |
| Formato Instagram | 1080×1080 ou 1080×1350 |

---

## 9. Fases de Implementação

### Fase 1 — Fundação (agora)

**Objetivo:** Validar comportamento institucional do Abduh antes de qualquer infraestrutura complexa

**Entregáveis:**

- [ ] Repositório `asof-agent-squad` criado no GitHub
- [ ] Estrutura de pastas completa (`core/`, `agents/`, `templates/`, `evals/`, `docs/`)
- [ ] `abduh-soul.md` — identidade e regras soberanas
- [ ] `abduh-router.md` — lógica de roteamento
- [ ] `abduh-policy.md` — regras institucionais
- [ ] `abduh-response-style.md` — tom e linguagem
- [ ] `abduh-quality-gate.md` — checklist pré-entrega
- [ ] 8 agentes especializados em `agents/`
- [ ] Templates base em `templates/`
- [ ] Casos de teste em `evals/casos-teste.md`
- [ ] Execução via Claude Project (arquivos como contexto) para validação

**Critério de aceite:**

- O Abduh rota corretamente 10 casos de teste reais da ASOF
- A linguagem está dentro do tom institucional esperado
- O quality gate bloqueia ao menos 2 casos de risco nos testes

**Critério de promoção para Fase 2:**

- 10/10 casos de teste passando sem regressão
- 0 regressões em 5 dias consecutivos de uso real
- `abduh-router.md` cobre 100% dos tipos de demanda identificados
- Ao menos 1 usuário além do arquiteto validou o comportamento
- `AGENT-CONTRACT.md` respeitado por todos os 8 agentes nos testes

### Fase 2 — Hermes Runtime

**Objetivo:** Migrar do Claude Project para o Hermes-Agent com memória persistente e gateway Telegram

**Entregáveis:**

- [ ] Hermes-Agent instalado em VPS
- [ ] Perfil `abduh-asof` configurado
- [ ] `SOUL.md` copiado para `~/.hermes/profiles/abduh-asof/SOUL.md`
- [ ] Skills ASOF instaladas no perfil
- [ ] Gateway Telegram funcional
- [ ] Cron jobs para rotinas recorrentes (resumos, notificações)
- [ ] Logs e sessões operacionais

**Critério de aceite:**

- Abduh responde via Telegram com o mesmo comportamento da Fase 1
- Memória persiste entre sessões distintas
- Ao menos 1 cron job ativo (ex: resumo semanal de demandas)

### Fase 3 — OpenClaw (opcional)

**Objetivo:** Adicionar braço técnico/devops isolado da camada institucional

**Entregáveis:**

- [ ] OpenClaw instalado em ambiente separado (não compartilhado com Abduh)
- [ ] Agentes técnicos mapeados (`code-review`, `deploy-check`, `vps-maintenance`)
- [ ] Protocolo de acionamento pelo Abduh definido
- [ ] RBAC mínimo configurado

**Critério de aceite:**

- Usuário institucional nunca fala diretamente com OpenClaw
- Comandos técnicos são sempre mediados pelo Abduh

### Fase 4 — Intranet ASOF

**Objetivo:** Expor o Abduh via interface web para funcionários e diretoria

**Entregáveis:**

- [ ] Interface simples integrada à intranet ASOF existente
- [ ] Abduh como backend lógico da interface
- [ ] Ações expostas (não "agentes"): `Criar ofício`, `Revisar comunicado`, `Gerar nota`, etc.
- [ ] RBAC por perfil (funcionário / diretoria)

---

## 10. Critérios de Qualidade

### 10.1 Comportamento institucional

- Abduh nunca inventa fato, norma ou data
- Toda saída com risco jurídico sinaliza necessidade de validação humana
- Tom mantém sobriedade institucional em 100% das respostas

### 10.2 Segurança

- Dados de associados nunca passam por serviços externos sem análise de LGPD
- Nenhuma ação destrutiva em produção sem confirmação explícita
- Secrets nunca expostos em logs, respostas ou contexto de agente

### 10.3 Manutenibilidade

- Todos os agentes versionados em Git com mensagens de commit descritivas
- Mudanças de comportamento documentadas em `CHANGELOG.md`
- Decisões arquiteturais registradas como ADRs em `docs/decisoes-arquiteturais/`

---

## 11. Riscos e Mitigações

| Risco | Probabilidade | Impacto | Mitigação |
|---|---|---|---|
| Abduh inventa norma jurídica | Médio | Alto | `abduh-quality-gate.md` obrigatório; agente `asof-legal-institutional` com regra explícita de não-invenção |
| Exposição de dados de associados | Baixo | Muito alto | `asof-governance-security.md` sempre acionado em demandas com dados pessoais |
| Tom inadequado em comunicado público | Médio | Alto | `abduh-response-style.md` + `asof-communications-chief.md` com princípios obrigatórios |
| Ação destrutiva sem confirmação | Baixo | Muito alto | Regra soberana #8 no `abduh-policy.md`; confirmação explícita obrigatória |
| Hermes-Agent com breaking changes | Médio | Médio | Versionamento de perfil no GitHub; rollback via Git |
| Expansão prematura para OpenClaw | Médio | Médio | OpenClaw bloqueado para Fase 3; sem acesso institucional direto |

---

## 12. Decisões Arquiteturais Registradas

### ADR-001: Hermes-Agent como runtime principal

**Decisão:** Abduh roda no Hermes-Agent, não no OpenClaw  
**Justificativa:** Hermes foi projetado para agente institucional permanente com memória, skills e identidade. OpenClaw é mais forte como executor técnico/devops  
**Consequências:** OpenClaw relegado a braço técnico opcional (Fase 3+)

### ADR-002: Agentes como módulos Markdown, não microserviços

**Decisão:** Os 8 agentes especializados são arquivos `.md` carregados no contexto do Abduh  
**Justificativa:** Simplicidade de manutenção, versionamento Git nativo, sem overhead de orquestração distribuída na Fase 1  
**Consequências:** Roteamento é implícito (contexto), não explícito (chamada de serviço); adequado para Fase 1, reavaliar na Fase 3

### ADR-003: Claude Project para validação antes do Hermes

**Decisão:** Fase 1 usa Claude Project com arquivos como contexto  
**Justificativa:** A parte difícil é acertar comportamento, não infraestrutura; validar primeiro, migrar depois  
**Consequências:** Sem memória persistente na Fase 1; comportamento validado antes de investir em stack

### ADR-004: Ponto único de entrada — usuário conversa apenas com Abduh

**Decisão:** Usuário conversa apenas com Abduh; agentes especializados não respondem diretamente
**Justificativa:** Elimina necessidade de roteamento manual; preserva voz institucional única
**Consequências:** Abduh precisa de `abduh-router.md` robusto; agentes precisam de saídas padronizadas

### ADR-005: Contrato de interface obrigatório entre agentes

**Decisão:** Todo agente especialista deve retornar saída conforme envelope definido em `agents/AGENT-CONTRACT.md`
**Justificativa:** Sem contrato, a consolidação pelo Abduh é inconsistente; formatos diferentes por agente tornam a síntese frágil à medida que os agentes evoluem
**Consequências:** Cada agente `.md` precisa de seção `## Saída` seguindo o contrato; o Abduh pode validar o envelope antes de consolidar

---

## 13. Glossário

| Termo | Definição |
|---|---|
| Abduh | Nome técnico do agente coordenador institucional da ASOF |
| Assistente ASOF | Nome institucional do Abduh (exibido para usuários) |
| SOUL.md | Arquivo de identidade, missão e regras soberanas do agente no Hermes |
| Squad | Conjunto dos 8 agentes especializados que operam nos bastidores |
| Quality gate | Checklist obrigatório antes de qualquer resposta do Abduh |
| OFC | Oficial de Chancelaria do Serviço Exterior Brasileiro |
| MRE | Ministério das Relações Exteriores |
| LGPD | Lei Geral de Proteção de Dados (Lei nº 13.709/2018) |
| ADR | Architecture Decision Record — registro de decisão arquitetural |
| VPS | Virtual Private Server — servidor de hospedagem próprio |

---

*PRD gerado em 2026-06-16. Versionar no repositório `asof-agent-squad` como `PRD.md`.*
