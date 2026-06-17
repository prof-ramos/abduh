# Guia do Desenvolvedor — Abduh / ASOF

## 1. Configuração do Ambiente

### 1.1 Pré-requisitos

- Python 3.11+
- `pip` ou `uv` (gerenciador de pacotes rápido)
- Conta na Anthropic com API key para Claude
- Bot do Telegram criado via [@BotFather](https://t.me/BotFather) (Fase 2+)
- Git

### 1.2 Instalação do Hermes-Agent

```bash
# Instalar o Hermes-Agent
pip install hermes-agent

# Setup interativo inicial (cria ~/.hermes/ com config padrão)
hermes setup
```

### 1.3 Criar o perfil Abduh

Cada perfil Hermes é um `HERMES_HOME` totalmente independente — com config, memória, skills, gateway e logs próprios. O perfil `abduh-asof` isola o Abduh de qualquer outro agente na mesma máquina.

```bash
# Criar o perfil (comando wrapper + diretório isolado)
hermes profile create abduh-asof

# Configurar API key e modelo (interativo)
abduh-asof setup
```

Isso cria a estrutura em `~/.hermes/profiles/abduh-asof/`:

```
~/.hermes/profiles/abduh-asof/
├── .env                    # API keys, bot tokens (chmod 600)
├── config.yaml             # Modelo, provider, toolsets, gateway, delegação
├── auth.json               # Credenciais OAuth
├── SOUL.md                 # Identidade e regras soberanas do Abduh
├── memories/               # Memória persistente (MEMORY.md, USER.md)
├── skills/                 # Skills ASOF versionadas
├── cron/                   # Rotinas recorrentes
├── sessions/               # Sessões do gateway (JSONL + snapshots)
├── state.db                # Store canônico de sessões (SQLite + FTS5)
└── logs/                   # Logs (errors.log, gateway.log — secrets redacted)
```

### 1.4 Configurar o modelo Claude

Editar `~/.hermes/profiles/abduh-asof/config.yaml`:

```yaml
model:
  provider: custom
  default: claude-sonnet-4-6        # modelo institucional padrão
  base_url: https://api.anthropic.com/v1   # ou proxy OpenAI-compatible
  api_mode: chat_completions
```

Adicionar a API key em `~/.hermes/profiles/abduh-asof/.env`:

```bash
OPENAI_API_KEY=sua-chave-aqui
# Se usar proxy próprio:
# OPENAI_BASE_URL=https://seu-proxy.com/v1
```

### 1.5 Configurar delegação (obrigatório para o Abduh)

O Abduh precisa de role `orchestrator` para usar `memory` e `send_message`. Adicionar ao `config.yaml`:

```yaml
delegation:
  orchestrator_enabled: true
  max_concurrent_children: 3
  max_spawn_depth: 2
  child_timeout_seconds: 300
  subagent_auto_approve: false
  inherit_mcp_toolsets: true
  max_iterations: 50
```

### 1.6 Instalar o SOUL.md do Abduh

Copiar o conteúdo de `core/abduh-soul.md` (quando existir) ou `abduh-soul.md` (estado atual) para o perfil:

```bash
cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md
```

O Hermes carrega `SOUL.md` apenas do `HERMES_HOME`. Ele **não** procura no diretório de trabalho. Se o arquivo estiver vazio, nada é injetado no prompt; se tiver conteúdo, é injetido verbatim após scanning e truncation.

### 1.7 Configurar o gateway Telegram (Fase 2+)

```bash
# Setup interativo (selecionar Telegram quando solicitado)
abduh-asof gateway setup
```

Ou configurar manualmente em `~/.hermes/profiles/abduh-asof/.env`:

```bash
TELEGRAM_BOT_TOKEN=123456789:ABCdefGHIjklMNOpqrSTUvwxYZ
TELEGRAM_ALLOWED_USERS=123456789    # IDs separados por vírgula
```

Para webhook mode (produção na VPS):

```bash
TELEGRAM_WEBHOOK_URL=https://seu-dominio.com/telegram
TELEGRAM_WEBHOOK_SECRET="$(openssl rand -hex 32)"
```

### 1.8 Verificar a instalação

```bash
# Diagnóstico completo do perfil
abduh-asof doctor

# Testar via CLI (sem gateway)
abduh-asof chat --verbose

# Ver status do gateway
abduh-asof gateway status

# Verificar modelo configurado
abduh-asof model
```

### 1.9 Comandos essenciais do perfil

```bash
abduh-asof chat                     # Conversar via CLI
abduh-asof chat --verbose           # Com logs detalhados
abduh-asof chat --toolsets web,file,terminal   # Toolsets específicos
abduh-asof gateway start            # Iniciar gateway Telegram
abduh-asof gateway stop             # Parar gateway
abduh-asof gateway status           # Status do gateway
abduh-asof doctor                   # Diagnóstico do perfil
abduh-asof model                    # Ver modelo configurado
```

---

## 2. Visão Geral da Estrutura do Projeto

### 2.1 Repositório (`asof-agent-squad`)

```
asof-agent-squad/
├── README.md
├── AGENTS.md                         # Índice de todos os agentes
├── CHANGELOG.md
├── PRD.md                            # Especificação completa do produto
├── core/
│   ├── abduh-soul.md                 # Identidade, missão, regras (SOUL.md)
│   ├── abduh-router.md               # Lógica de roteamento entre agentes
│   ├── abduh-policy.md              # Regras soberanas institucionais
│   ├── abduh-response-style.md      # Tom, linguagem, formato de saída
│   └── abduh-quality-gate.md         # Checklist pré-entrega obrigatório
├── agents/
│   ├── AGENT-CONTRACT.md             # Contrato de saída obrigatório
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
    ├── dev-guide.md                  # Este documento
    └── decisoes-arquiteturais/       # ADRs
```

### 2.2 Perfil Hermes (`~/.hermes/profiles/abduh-asof/`)

O perfil é o runtime. O repositório é a fonte da verdade. O deploy copia do repo para o perfil:

| Repo (fonte) | Perfil Hermes (runtime) | Propósito |
|---|---|---|
| `core/abduh-soul.md` | `SOUL.md` | Identidade e regras do agente |
| `agents/*.md` | carregados como contexto | Análise dos especialistas |
| `skills/` (a criar) | `skills/` | Skills ASOF instaladas |

### 2.3 Arquitetura do fluxo

```
Usuário (Telegram / CLI)
  → Hermes Gateway
  → Abduh (SOUL.md carrega identidade + regras)
  → Classifica demanda via abduh-router.md
  → Carrega max 3 agentes como contexto (Markdown)
  → Consolida análises (hierarquia de precedência)
  → Quality gate (abduh-quality-gate.md)
  → Resposta final institucional
```

Os 8 agentes especializados **não respondem diretamente** ao usuário. São módulos Markdown carregados no contexto do Abduh conforme o tipo de demanda.

### 2.4 Hierarquia de precedência dos agentes

```
1. Governance Security    ← LGPD e dados pessoais: prioridade absoluta
2. Legal Institutional    ← Risco jurídico antes de qualquer comunicação
3. Strategy Chief         ← Risco institucional/reputacional
4. Operations Chief       ← Processo e execução
5. Communications Chief   ← Formatação e linguagem final
6. Membership Relations   ← Relacionamento e convênios
7. Digital Architect      ← Decisões técnicas
8. AI Automation Chief    ← Automação e IA
```

---

## 3. Fluxo de Trabalho de Desenvolvimento

### 3.1 Fase 1 — Validação do comportamento (estado atual)

A Fase 1 valida o comportamento institucional do Abduh antes de qualquer infraestrutura. O runtime é um Claude Project com os arquivos Markdown como contexto.

**Workflow:**

1. Editar arquivos Markdown no repositório (`core/`, `agents/`, `templates/`)
2. Testar via Claude Project (colar arquivos como contexto)
3. Validar roteamento, tom e quality gate com casos de teste
4. Commitar mudanças com mensagens descritivas
5. Atualizar `CHANGELOG.md` para mudanças de comportamento

**Ciclo de edição de um agente:**

```
1. Editar agents/asof-<nome>.md
2. Verificar se o frontmatter está correto:
   ---
   agent: asof-<nome>
   version: x.y.z
   last_updated: YYYY-MM-DD
   ---
3. Verificar se a saída segue o AGENT-CONTRACT.md:
   - Nível de confiança: [alto | médio | baixo]
   - Requer validação humana: [sim | não]
   - Riscos identificados: [lista ou "nenhum"]
4. Testar com um caso de teste em evals/casos-teste.md
5. Commitar com mensagem descritiva (PATCH/MINOR/MAJOR segundo AGENT-CONTRACT.md)
```

**Versionamento de agentes (conforme AGENT-CONTRACT.md):**

| Tipo de mudança | Versão | Ação |
|---|---|---|
| Correção de texto | PATCH | Commit direto |
| Nova regra ou campo de saída | MINOR | Commit + atualizar `last_updated` |
| Mudança de escopo | MAJOR | Commit + atualizar router + novo ciclo de evals |

### 3.2 Fase 2 — Migrar para Hermes-Agent

Quando o comportamento estiver validado (10/10 casos de teste, 0 regressões em 5 dias):

1. Copiar `core/abduh-soul.md` para `~/.hermes/profiles/abduh-asof/SOUL.md`
2. Instalar skills ASOF no perfil
3. Configurar gateway Telegram
4. Validar que o comportamento no Hermes é idêntico ao Claude Project
5. Configurar cron jobs (ex: resumo semanal de demandas)

### 3.3 Memória persistente

O Hermes oferece memória persistente via `MEMORY.md` e `USER.md`:

- **`MEMORY.md`** — Notas do agente sobre o ambiente, convenções do projeto, fatos aprendidos. Limite: ~2200 chars por entrada.
- **`USER.md`** — Preferências e estilo de comunicação do usuário. Limite: ~1375 chars por entrada.

Uso via ferramenta `memory`:

```python
memory("add", "Decisão ASOF: ofícios seguem Manual de Redação da Presidência")
memory("add", "Diretoria atual: Presidente = Emb. Silva, Vice = Min. Santos")
memory("replace", "Diretoria atual", "Diretoria atual: Presidente = Emb. Costa, Vice = Min. Lima")
memory("remove", "informação desatualizada")
```

Não há ação `read` — o conteúdo da memória é injetado automaticamente no system prompt no início de cada sessão.

**Para o Abduh:** usar memória para preservar decisões institucionais, convenções de formatação da ASOF, e preferências do operador. Nunca armazenar dados pessoais de associados em memória.

---

## 4. Abordagem de Teste

### 4.1 Casos de teste (Fase 1)

Os testes na Fase 1 são manuais — não há runtime automatizado. O arquivo `evals/casos-teste.md` define os cenários de teste.

**Cada caso de teste deve especificar:**

```yaml
id: CT-001
tipo: roteamento
demanda: "Precisamos enviar um ofício ao MRE sobre a revisão do regulamento interno"
agentes_esperados:
  obrigatórios: [legal-institutional, strategy-chief]
  condicionais: [communications-chief]  # se versão pública
validacao_humana: true
risco: jurídico + institucional
```

### 4.2 Critérios de aceite da Fase 1

| Critério | Detalhe |
|---|---|
| Roteamento correto | 10/10 casos de teste reais da ASOF |
| Tom institucional | Linguagem dentro do padrão ASOF em todas as respostas |
| Quality gate funcional | Bloqueia ao menos 2 casos de risco nos testes |
| Contrato respeitado | Todos os 8 agentes incluem os 3 campos obrigatórios |
| Sem regressão | 5 dias consecutivos de uso real sem regressão |

### 4.3 Contrato de saída dos agentes

Todo agente deve incluir ao final da análise:

```
Nível de confiança: [alto | médio | baixo]
Requer validação humana: [sim | não]
Riscos identificados: [lista ou "nenhum"]
```

**Comportamento do Abduh por sinalização:**

| Sinalização | Ação do Abduh |
|---|---|
| `confiança: baixo` | Não entregar; solicitar mais contexto ao usuário |
| `requer validação: sim` | Adicionar `⚠️ Requer validação antes de uso` na resposta |
| `riscos identificados` com itens | Incluir bloco de riscos antes da saída principal |

### 4.4 Quality gate

O quality gate é obrigatório antes de **qualquer** resposta. Nunca é ignorado. Os 7 itens de verificação estão em `core/abduh-quality-gate.md` (atualmente inline na seção 8 do `abduh-soul.md`).

Quando em dúvida sobre qualquer item, tratar como SIM.

### 4.5 Validação de regressão (Fase 2+)

Na Fase 2, quando o Hermes-Agent estiver operacional, os casos de teste podem ser executados via CLI:

```bash
# Enviar demanda de teste via CLI
abduh-asof chat --query "Precisamos enviar um ofício ao MRE sobre a revisão do regulamento interno"

# Com toolsets específicos (se necessário)
abduh-asof chat --toolsets web,file
```

---

## 5. Solução de Problemas Comuns

### 5.1 Diagnóstico

```bash
# Diagnóstico completo do perfil
abduh-asof doctor

# Ver modelo configurado
abduh-asof model

# Ver sessions existentes
abduh-asof sessions list

# Ver status do gateway
abduh-asof gateway status

# Continuar última sessão
abduh-asof --continue
```

### 5.2 Logs

```bash
# Últimas 50 linhas do log do agente (padrão)
abduh-asof logs

# Seguir log em tempo real
abduh-asof logs -f

# Últimas 100 linhas do gateway
abduh-asof logs gateway -n 100

# Filtrar warnings e errors da última hora
abduu-asof logs --level WARNING --since 1h

# Filtrar por sessão específica
abduh-asof logs --session abc123

# Seguir errors dos últimos 30 minutos
abduh-asof logs errors --since 30m -f

# Listar todos os arquivos de log com tamanhos
abduh-asof logs list

# Buscar erros manualmente no gateway
grep -i "failed to send\|error" ~/.hermes/profiles/abduh-asof/logs/gateway.log | tail -20
```

### 5.3 Problemas comuns

| Problema | Causa provável | Solução |
|---|---|---|
| Perfil não aparece em `hermes profile list` | Perfil não foi criado ou nome incorreto | `hermes profile create abduh-asof` |
| Gateway não inicia | `.env` sem `TELEGRAM_BOT_TOKEN` ou token inválido | Verificar `.env`; testar token com `abduh-asof doctor` |
| Tarefas ficam em READY indefinidamente | Nenhum worker online para o perfil | Verificar `abduh-asof gateway status` |
| Erro de permissão no workspace | Profile sem read+write no diretório | `chmod -R u+rw ~/.hermes/profiles/abduh-asof/` |
| Resposta fora do tom ASOF | `SOUL.md` desatualizado ou corrompido | Recopiar do repo: `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md` |
| Agente não inclui os 3 campos do contrato | Instrução ausente ou conflitante no `SOUL.md` | Verificar seção 9 do `SOUL.md` |
| Quality gate não é executado | Instrução no `SOUL.md` não está sendo seguida | Testar via `abduh-asof chat --verbose` e inspecionar o reasoning |
| Modelo não responde | API key invália ou modelo não configurado | `abduh-asof doctor` + `abduh-asof model` |
| Memória não persiste entre sessões | Perfil incorreto ou `memories/` sem permissão | Verificar `~/.hermes/profiles/abduh-asof/memories/` |

### 5.4 Recuperação de estado quebrado

```bash
# Diagnóstico completo
abduh-asof doctor

# Verificar modelo
abduh-asof model

# Reconfigurar (interativo)
abduh-asof setup

# Listar sessões e limpar se necessário
abduh-asof sessions list

# Continuar última sessão
abduh-asof --continue

# Verificar gateway
abduh-asof gateway status
```

### 5.5 Reinstalar o SOUL.md após mudanças no repo

Sempre que `core/abduh-soul.md` for atualizado no repositório:

```bash
cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md
```

O Hermes carrega `SOUL.md` no início de cada sessão. Não é necessário reiniciar o gateway para mudanças no `SOUL.md` — elas entram em vigor na próxima sessão.

### 5.6 Limite de contexto e compressão

O Hermes comprime automaticamente o contexto quando se aproxima do limite de tokens. Para modelos com janela menor:

```yaml
# Em config.yaml — ajustar file_read_max_chars
file_read_max_chars: 30000   # para modelos com 16K de contexto
# ou
file_read_max_chars: 200000  # para modelos com 200K+ de contexto
```

### 5.7 Skills ASOF

Para instalar uma skill customizada do repo ASOF:

```bash
# De um arquivo SKILL.md local
abduh-asof skills install /caminho/para/skill/SKILL.md

# De uma URL direta
abduh-asof skills install https://raw.githubusercontent.com/org/asof-agent-squad/main/skills/nome/SKILL.md

# De um repositório GitHub
abduh-asof skills install org/asof-agent-squad

# Listar skills instaladas
abduh-asof skills list

# Verificar updates
abduh-asof skills check

# Atualizar
abduh-asof skills update
```

---

## Referência Rápida

| Ação | Comando |
|---|---|
| Criar perfil | `hermes profile create abduh-asof` |
| Configurar perfil | `abduh-asof setup` |
| Chat via CLI | `abduh-asof chat` |
| Chat verbose | `abduh-asof chat --verbose` |
| Diagnóstico | `abduh-asof doctor` |
| Iniciar gateway | `abduh-asof gateway start` |
| Parar gateway | `abduh-asof gateway stop` |
| Status do gateway | `abduh-asof gateway status` |
| Ver modelo | `abduh-asof model` |
| Ver logs | `abduh-asof logs -f` |
| Instalar skill | `abduh-asof skills install <url>` |
| Listar skills | `abduh-asof skills list` |
| Copiar SOUL.md | `cp core/abduh-soul.md ~/.hermes/profiles/abduh-asof/SOUL.md` |
