# STATUS.md — Abduh / ASOF

**Última atualização:** 2026-06-17 16:30 BRT
**Branch atual:** `feat/best-practices-gemini-setup`
**PR:** [https://github.com/prof-ramos/abduh/pull/1](https://github.com/prof-ramos/abduh/pull/1)

---

## 1. Infraestrutura — VPS

| Item | Detalhe |
|---|---|
| **Provedor** | Tencent Cloud (VM-0-9-ubuntu) |
| **IP público** | `43.173.94.254` |
| **IP Tailscale** | `100.93.133.102` |
| **OS** | Ubuntu 24.04, kernel `6.8.0-124-generic` |
| **Timezone** | `America/Sao_Paulo` (-03:00) |
| **Senha** | `Alcione2025**` |

### Acesso SSH

```bash
# Via alias configurado (recomendado)
ssh abduh-asof

# Via IP direto
ssh ubuntu@43.173.94.254

# Acessar tmux (3 janelas: hermes, logs, cli)
ssh abduh-asof -t "tmux attach -t abduh"

# Comando único
ssh abduh-asof "sudo systemctl status hermes-gateway --no-pager"
```

### Serviços rodando

| Serviço | Status | Tipo |
|---|---|---|
| `hermes-gateway` | ✅ Ativo | systemd (boot) |
| `tailscaled` | ✅ Conectado | systemd |
| `sshd` | ✅ Ativo | systemd |
| `tmux` (sessão `abduh`) | ✅ 3 janelas | usuário |

### Hermes-Agent na VPS

| Item | Detalhe |
|---|---|
| **Versão** | v0.16.0 |
| **Instalação** | `~/hermes-env/` (virtualenv Python 3.12.3) |
| **PATH** | `~/hermes-env/bin/hermes` (não via sudo) |
| **Gateway** | systemd service, roda como `ubuntu`, inicia no boot |
| **SOUL.md** | Sincronizado do repositório (266 linhas) |
| **Profile** | `abduh-asof` → modelo Gemini 2.0 Flash |
| **Config** | `~/.hermes/profiles/abduh-asof/config.yaml` |
| **API Key** | Gemini configurada (via `.env` no profile) |
| **GitHub** | Não configurado na VPS (repo só no Mac) |

### Configuração do modelo (Gemini)

```yaml
model:
  provider: custom
  default: gemini-2.0-flash-exp
  base_url: https://generativelanguage.googleapis.com/v1beta/openai/
  api_mode: chat_completions
```

---

## 2. Projeto Local — Estrutura de Arquivos

```
abduh/
├── README.md              # Visão geral, quickstart
├── CLAUDE.md              # Instruções para Claude Code
├── PRD-abduh-asof.md      # Spec completa do produto (v0.3.0)
├── abduh-soul.md          # Identidade do Abduh (266 linhas)
├── AGENT-CONTRACT.md      # Contrato de saída dos agentes
├── .markdownlint.json     # Regras de lint para Markdown
├── .hermes/
│   └── profiles/abduh-asof/
│       ├── .env.example         # Variáveis de ambiente (exemplo)
│       └── config.example.yaml  # Config Hermes (exemplo)
├── docs/
│   ├── dev-guide.md             # Guia do desenvolvedor
│   ├── best-practices-check.md  # Revisão de melhores práticas (Context7)
│   └── gemini-setup-guide.md    # Guia de configuração Gemini
├── .claude/
│   └── settings.local.json
└── .verboo/
    └── settings.local.json
```

### O que está faltando (PRD Fase 1)

```
core/                          # ⬜ NÃO criado ainda
├── abduh-soul.md              # Identidade (extrair do abduh-soul.md)
├── abduh-policy.md            # Regras soberanas (seção 5)
├── abduh-router.md            # Roteamento de agentes (seção 7)
├── abduh-response-style.md    # Tom e linguagem (seção 6)
└── abduh-quality-gate.md      # Quality gate (seção 8)

agents/                        # ⬜ NÃO criado ainda (8 arquivos)
├── asof-strategy-chief.md     # Estratégia e risco institucional
├── asof-operations-chief.md   # Processos e fluxos
├── asof-communications-chief.md   # Comunicação institucional
├── asof-legal-institutional.md    # Jurídico-institucional
├── asof-membership-relations.md   # Associados e convênios
├── asof-digital-architect.md      # Tecnologia e intranet
├── asof-governance-security.md    # LGPD e segurança
└── asof-ai-automation-chief.md    # IA e automação

templates/                     # ⬜ NÃO criado ainda (4 arquivos)
├── oficio.md
├── nota-institucional.md
├── comunicado-associados.md
└── relatorio-diretoria.md

evals/                         # ⬜ NÃO criado ainda
└── casos-teste.md             # 13 casos de teste
```

---

## 3. Status dos PRs

| PR | Branch | Status | Link |
|---|---|---|---|
| #1 — Best practices + Gemini setup | `feat/best-practices-gemini-setup` | ✅ Aberto (não mergeado) | [PR #1](https://github.com/prof-ramos/abduh/pull/1) |

---

## 4. Próximos Passos (Priorizados)

### 🔴 Imediatos (Fase 1 — completar fundação)

| # | O quê | Esforço |
|---|---|---|
| 1 | Extrair `core/abduh-policy.md` do `abduh-soul.md` (seção 5) | ~5 min |
| 2 | Extrair `core/abduh-router.md` do `abduh-soul.md` (seção 7 + 10) | ~10 min |
| 3 | Extrair `core/abduh-response-style.md` do `abduh-soul.md` (seção 6) | ~5 min |
| 4 | Extrair `core/abduh-quality-gate.md` do `abduh-soul.md` (seção 8) | ~5 min |
| 5 | Extrair `core/abduh-soul.md` (seções 1-4, 9, 11-12) | ~10 min |
| 6 | Atualizar `abduh-soul.md` raiz para virar ponteiro | ~5 min |
| 7 | Criar 8 agentes em `agents/` | ~40 min |
| 8 | Criar 4 templates em `templates/` | ~20 min |
| 9 | Criar `evals/casos-teste.md` (13 casos) | ~20 min |
| 10 | Validar com Claude Project (casos de teste) | ~30 min |

### 🟡 Curto prazo

| # | O quê | Motivo |
|---|---|---|
| 11 | Atualizar `CLAUDE.md` para refletir nova estrutura | Manutenção |
| 12 | Atualizar `README.md` com estrutura `core/`, `agents/` | Manutenção |
| 13 | Fazer merge do PR #1 na main | Organização |
| 14 | Criar CHANGELOG.md | Rastreabilidade |

### 🟢 Médio prazo (preparação Fase 2)

| # | O quê | Motivo |
|---|---|---|
| 15 | Configurar webhook Telegram na VPS (HTTPS + secret token) | Produção |
| 16 | Criar Dockerfile + docker-compose.yml | Produção |
| 17 | Implementar tratamento de erros da API Gemini | Resiliência |
| 18 | Configurar backup automático | Segurança |
| 19 | Implementar health check endpoint | Monitoring |

---

## 5. Comandos Úteis

### Local (Mac)

```bash
# Status do projeto
cd ~/projetos/ASOF/abduh
git status

# Syncing SOUL.md para a VPS
scp abduh-soul.md abduh-asof:~/.hermes/profiles/abduh-asof/SOUL.md

# Testar via Hermes CLI
abduh-asof chat -q "Ola, quem e voce?"
```

### VPS

```bash
# Gateway
ssh abduh-asof "sudo systemctl status hermes-gateway"

# Logs do gateway
ssh abduh-asof -t "sudo journalctl -u hermes-gateway -f"

# Anexar tmux
ssh abduh-asof -t "tmux attach -t abduh"

# Testar Gemini
ssh abduh-asof "echo 'Ola' | timeout 30 ~/hermes-env/bin/hermes chat -q 'Ola'"

# Hermes doctor
ssh abduh-asof "export PATH=\"\$HOME/hermes-env/bin:\$HOME/.local/bin:\$PATH\" && hermes doctor"
```

---

## 6. Notas Técnicas

### SSH Key

Chave ED25519 gerada em `~/.ssh/abduh-asof` (pública + privada), instalada na VPS em `authorized_keys`. Config em `~/.ssh/config`:

```
Host abduh-asof Abduh Abduh-ASOF
    HostName 43.173.94.254
    User ubuntu
    IdentityFile ~/.ssh/abduh-asof
    IdentitiesOnly yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
    SetEnv TERM=xterm-256color
```

### Hermes Gateway Logs

O gateway roda como serviço systemd. Logs via `journalctl`:

```bash
# Tempo real
sudo journalctl -u hermes-gateway -f

# Últimas 50 linhas
sudo journalctl -u hermes-gateway --no-pager -n 50

# Desde o último boot
sudo journalctl -u hermes-gateway --no-pager -b
```

### Caminhos importantes na VPS

```bash
~/.hermes/                    # Hermes home
~/.hermes/profiles/abduh-asof/  # Profile Abduh
~/.hermes/SOUL.md             # SOUL.md (link p/ profile)
~/.hermes/config.yaml         # config (link p/ profile)
~/.hermes/.env                # .env (link p/ profile)
~/hermes-env/bin/hermes       # Binário Hermes
/etc/systemd/system/hermes-gateway.service  # Serviço systemd
```

---

## 7. Glossário

| Termo | Significado |
|---|---|
| **Abduh** | Assistente institucional da ASOF |
| **ASOF** | Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro |
| **Hermes-Agent** | Runtime do agente (NousResearch) |
| **SOUL.md** | Documento de identidade e regras soberanas do Abduh |
| **Quality Gate** | Checklist pré-entrega que nunca é ignorado |
| **OMC** | oh-my-claudecode — orquestrador multi-agente local |
| **MCP** | Model Context Protocol — protocolo de ferramentas externas |
| **Context7** | MCP para consultar documentação oficial de bibliotecas |
