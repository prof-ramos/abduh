# Abduh — Assistente Institucional da ASOF

Agente coordenador institucional permanente da ASOF (Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro). Orquestra internamente uma squad de 8 agentes especializados, aplica regras institucionais, preserva memória operacional e entrega resposta final com voz única e tom cauteloso.

**Não é um chatbot genérico.** É um assistente institucional persistente, com identidade, memória, skills e quality gate próprios.

## Stack

| Componente | Tecnologia |
|---|---|
| Agent runtime | Hermes-Agent (NousResearch) |
| LLM backend | Claude (Anthropic API, via proxy OpenAI-compatible) |
| Gateway | Telegram (Fase 1) |
| Infraestrutura | VPS + Docker + Traefik + Portainer |
| Versionamento | GitHub — repositório `asof-agent-squad` |

## Início rápido

```bash
# 1. Instalar Hermes-Agent
pip install hermes-agent
hermes setup

# 2. Criar perfil Abduh
hermes profile create abduh-asof
abduh-asof setup

# 3. Copiar SOUL.md para o perfil
cp abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md

# 4. Testar via CLI
abduh-asof chat --verbose

# 5. Diagnóstico
abduh-asof doctor
```

Consulte [`docs/dev-guide.md`](docs/dev-guide.md) para instruções completas de configuração, fluxo de trabalho, testes e solução de problemas.

## Documentação

| Arquivo | Conteúdo |
|---|---|
| [`PRD-abduh-asof.md`](PRD-abduh-asof.md) | Especificação completa do produto |
| [`abduh-soul.md`](abduh-soul.md) | Identidade, regras soberanas, tom, roteamento, quality gate |
| [`AGENT-CONTRACT.md`](AGENT-CONTRACT.md) | Contrato de saída obrigatório para todos os agentes |
| [`docs/dev-guide.md`](docs/dev-guide.md) | Guia do desenvolvedor |
| [`CLAUDE.md`](CLAUDE.md) | Orientações para Claude Code |

## Arquitetura

```
Usuário (Telegram / CLI)
  → Hermes Gateway
  → Abduh (SOUL.md carrega identidade + regras)
  → Classifica demanda → carrega max 3 agentes como contexto
  → Consolida análises (hierarquia de precedência)
  → Quality gate
  → Resposta final institucional
```

Os 8 agentes especializados são módulos Markdown carregados no contexto — não respondem diretamente ao usuário.

### Hierarquia de precedência

1. Governance Security (LGPD, dados — prioridade absoluta)
2. Legal Institutional (risco jurídico)
3. Strategy Chief (risco institucional/reputacional)
4. Operations Chief (processo/execução)
5. Communications Chief (formatação/linguagem)
6. Membership Relations (relacionamento/convênios)
7. Digital Architect (decisões técnicas)
8. AI Automation Chief (automação/IA)

## Fases

| Fase | Status | Objetivo |
|---|---|---|
| 1 — Fundação | **Atual** | Validar comportamento institucional via Claude Project |
| 2 — Hermes Runtime | Planejada | Migrar para Hermes-Agent com memória persistente + Telegram |
| 3 — OpenClaw | Opcional | Adicionar braço técnico/devops isolado |
| 4 — Intranet ASOF | Futura | Interface web para funcionários e diretoria |
