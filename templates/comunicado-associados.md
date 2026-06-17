---
template: comunicado-associados
version: 1.0.0
last_updated: 2026-06-17
---

# Template de Comunicado a Associados — ASOF

---

## Estrutura

### Cabeçalho

```
COMUNICADO ASOF Nº [NÚMERO]/[ANO]
[Local], [dia] de [mês] de [ano].
```

### Assunto

```
Assunto: [título conciso]
```

### Corpo

```
Prezado(a) associado(a),

1. [Contexto — o que motivou o comunicado]
2. [Informação — o que o associado precisa saber]
3. [Ação esperada — o que o associado deve fazer, se aplicável]

Para esclarecimento de dúvidas, entre em contato pelo e-mail
[contato] ou telefone [telefone].
```

### Fecho

```
Atenciosamente,

[Nome]
[Cargo]
ASOF
```

---

## Campos Preenchíveis

| Campo | Exemplo | Obrigatório |
|---|---|---|
| `[NÚMERO]` | 005 | sim |
| `[ANO]` | 2026 | sim |
| `[Local]` | Brasília | sim |
| `[dia]` | 17 | sim |
| `[mês]` | junho | sim |
| `[Assunto]` | Novo convênio com o Banco do Brasil | sim |
| `[Contexto]` | Parágrafo introdutório | sim |
| `[Informação]` | Parágrafo informativo | sim |
| `[Ação esperada]` | Parágrafo de ação | condicional |
| `[Contato]` | <asof@asof.org.br> | condicional |
| `[Telefone]` | (61) 3224-1234 | condicional |
| `[Nome]` | Manuel Alves Bezerra | sim |
| `[Cargo]` | Presidente | sim |

---

## Regras de Estilo

1. **Tom** — cordial mas institucional
2. **LGPD** — não incluir dados pessoais sensíveis no comunicado geral
3. **Validação** — revisar com Membership Relations antes de enviar
4. **Versão pública** — se o comunicado for divulgado externamente, revisar com Communications Chief

---

## Quality Gate

- [ ] Dados pessoais de associados expostos?
- [ ] Tom cordial e institucional?
- [ ] Informação clara e ação esperada explícita?
- [ ] Contato para dúvidas incluído?
- [ ] Requer validação humana?
