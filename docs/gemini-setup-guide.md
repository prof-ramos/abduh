# Guia de Configuração — Gemini API com Hermes-Agent

**Objetivo:** Configurar o perfil Hermes-Agent `abduh-asof` para usar a API do Gemini via proxy OpenAI-compatible.

---

## 1. Pré-requisitos

- ✅ Hermes-Agent instalado: `pip install hermes-agent`
- ✅ Perfil criado: `hermes profile create abduh-asof`
- ✅ Chave da API Gemini: <https://makersuite.google.com/app/apikey>

---

## 2. Configuração Rápida (Fase 1 - Validação)

### 2.1 Criar perfil Hermes

```bash
hermes profile create abduh-asof
```

### 2.2 Configurar API key do Gemini

```bash
# Criar arquivo .env no perfil
cd ~/.hermes/profiles/abduh-asof
cat > .env << EOF
GEMINI_API_KEY=AIzaSySUA_CHAVE_AQUI
EOF

# Definir permissões restritas
chmod 600 .env
```

### 2.3 Configurar modelo no config.yaml

```bash
cd ~/.hermes/profiles/abduh-asof
cat >> config.yaml << EOF

model:
  provider: custom
  default: gemini-2.0-flash-exp
  base_url: https://generativelanguage.googleapis.com/v1beta/openai/
  api_mode: chat_completions
EOF
```

### 2.4 Testar via CLI

```bash
# Testar conexão
abduh-asof chat --verbose

# Verificar configuração
abduh-asof model
```

---

## 3. Configuração Completa (Fase 2 - Produção)

### 3.1 Copiar arquivos de exemplo

```bash
cd ~/.hermes/profiles/abduh-asof

# Copiar .env.example
cp .env.example .env

# Copiar config.example.yaml
cp config.example.yaml config.yaml
```

### 3.2 Preencher .env com suas credenciais

```bash
# Editar .env
nano .env
```

Preencha as variáveis:

- `GEMINI_API_KEY` — Sua chave da API Gemini
- `TELEGRAM_BOT_TOKEN` — Token do seu bot do Telegram
- `TELEGRAM_ALLOWED_USERS` — IDs dos usuários permitidos
- `TELEGRAM_WEBHOOK_URL` — URL HTTPS do seu servidor
- `TELEGRAM_WEBHOOK_SECRET` — Token secreto gerado

### 3.3 Configurar webhooks do Telegram (Fase 2)

```bash
# Gerar token secreto
TELEGRAM_WEBHOOK_SECRET="$(openssl rand -hex 32)"

# Configurar webhook
curl -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/setWebhook" \
  -H "Content-Type: application/json" \
  -d "{\"url\": \"${TELEGRAM_WEBHOOK_URL}\", \"secret_token\": \"${TELEGRAM_WEBHOOK_SECRET}\"}"
```

### 3.4 Iniciar gateway

```bash
# Iniciar gateway Telegram
abduh-asof gateway start

# Verificar status
abduh-asof gateway status
```

---

## 4. Modelos Gemini Disponíveis

| Modelo | Velocidade | Qualidade | Uso Recomendado |
|---|---|---|---|
| `gemini-2.0-flash-exp` | ⚡⚡⚡⚡⚡ | ⭐⭐⭐⭐ | Fase 1, conversação rápida |
| `gemini-1.5-flash` | ⚡⚡⚡⚡ | ⭐⭐⭐⭐ | Fase 2, balanceado |
| `gemini-1.5-pro` | ⚡⚡ | ⭐⭐⭐⭐⭐ | Fase 2, alta qualidade |

### 4.1 Mudar de modelo

Edite `config.yaml`:

```yaml
model:
  provider: custom
  default: gemini-1.5-pro  # Altere aqui
  base_url: https://generativelanguage.googleapis.com/v1beta/openai/
  api_mode: chat_completions
```

---

## 5. Troubleshooting

### 5.1 Erro: "API key inválida"

```bash
# Verificar .env
cat ~/.hermes/profiles/abduh-asof/.env | grep GEMINI_API_KEY

# Testar API key diretamente
curl "https://generativelanguage.googleapis.com/v1beta/openai/models" \
  -H "Authorization: Bearer $GEMINI_API_KEY"
```

### 5.2 Erro: "Model not found"

```bash
# Verificar modelo no config.yaml
abduh-asof model

# Verificar modelos disponíveis no proxy
curl "https://generativelanguage.googleapis.com/v1beta/openai/models" \
  -H "Authorization: Bearer $GEMINI_API_KEY"
```

### 5.3 Erro: "Rate limit exceeded"

```bash
# Aumentar timeout no config.yaml
model:
  timeout: 300  # 5 minutos

# Implementar backoff exponencial (Fase 2)
```

### 5.4 Erro: "Webhook failed"

```bash
# Verificar URL HTTPS
curl -I $TELEGRAM_WEBHOOK_URL

# Verificar logs
tail -f ~/.hermes/profiles/abduh-asof/logs/gateway.log

# Remover webhook anterior
curl -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/deleteWebhook"
```

---

## 6. Segurança

### 6.1 Permissões do .env

```bash
# Sempre definir permissões restritas
chmod 600 ~/.hermes/profiles/abduh-asof/.env

# Verificar permissões
ls -la ~/.hermes/profiles/abduh-asof/.env
```

### 6.2 Não commitar .env no Git

```bash
# Adicionar ao .gitignore
echo ".env" >> ~/.hermes/profiles/abduh-asof/.gitignore
```

### 6.3 Rotacionar API keys

```bash
# Gerar nova chave em: https://makersuite.google.com/app/apikey
# Atualizar .env
# Reiniciar gateway
abduh-asof gateway restart
```

---

## 7. Alternativas

### 7.1 Usar Claude (Anthropic)

```bash
# .env
ANTHROPIC_API_KEY=sua-chave-aqui

# config.yaml
model:
  provider: anthropic
  default: claude-sonnet-4-6
  api_mode: chat_completions
```

### 7.2 Usar OpenAI

```bash
# .env
OPENAI_API_KEY=sua-chave-aqui

# config.yaml
model:
  provider: openai
  default: gpt-4o
  api_mode: chat_completions
```

---

## 8. Referências

- [Documentação Hermes-Agent](https://github.com/NousResearch/hermes-agent)
- [API Gemini](https://ai.google.dev/gemini-api/docs)
- [Proxy OpenAI-compatible do Gemini](https://ai.google.dev/gemini-api/docs/openai)
- [Telegram Bot API](https://core.telegram.org/bots/api)

---

*Atualizado em 2026-06-17 para usar Gemini API.*
