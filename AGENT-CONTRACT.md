# AGENT-CONTRACT.md

## Contrato de Interface — Squad ASOF

**Versão:** 1.1.0  
**Data:** 2026-06-16  
**Escopo:** Todos os agentes em `agents/`  
**Nota:** Este documento define a intenção arquitetural para saídas de agentes. A implementação formal (envelope estruturado, validação programática) está prevista para Fase 2+, quando houver runtime que a suporte. Na Fase 1, os agentes seguem esta convenção por instrução no `abduh-soul.md`.

---

## 1. Propósito

Garantir que todo agente especialista retorne saída previsível o suficiente para o Abduh consolidar com consistência. Sem isso, a síntese depende de parsing de texto livre — frágil e impossível de auditar.

---

## 2. Convenção de saída (Fase 1)

Todo agente deve incluir, ao final de sua análise, três sinalizações obrigatórias:

```
Nível de confiança: [alto | médio | baixo]
Requer validação humana: [sim | não]
Riscos identificados: [lista de riscos, ou "nenhum"]
```

**Nível de confiança:**

| Valor | Significado |
|---|---|
| `alto` | Contexto completo, sem ambiguidade, análise direta |
| `médio` | Lacunas de informação ou premissas assumidas |
| `baixo` | Contexto insuficiente; análise é especulativa |

Quando `baixo`, o Abduh não entrega sem pedir informações adicionais ao usuário.

**Requer validação humana:**  
`sim` sempre que envolver risco jurídico, dado pessoal, comunicado público ou ação em produção. Em dúvida, usar `sim`.

**Riscos identificados:**  
Frases completas descrevendo o risco específico. Exemplo:

```
- Norma citada pode estar desatualizada — verificar DOU antes de usar
- Comunicado menciona dado de associado específico — avaliar anonimização
```

---

## 3. O que o Abduh faz com essas sinalizações

- `confiança baixo` → solicita mais contexto antes de entregar
- `requer validação: sim` → adiciona `⚠️ Requer validação antes de uso` na resposta final
- `riscos identificados` com itens → inclui bloco de riscos na resposta, antes da saída principal

---

## 4. Versionamento dos agentes

Todo arquivo de agente deve ter frontmatter mínimo:

```yaml
---
agent: asof-[nome]
version: 1.0.0
last_updated: YYYY-MM-DD
---
```

Política simples:

- Correção de texto → `PATCH`
- Nova regra ou campo de saída → `MINOR`  
- Mudança de escopo → `MAJOR` (exige atualização do `abduh-router.md` e novo ciclo de evals)

---

*Este contrato evolui junto com o sistema. Formalização do envelope em YAML estruturado está prevista para Fase 2, quando o Hermes-Agent puder implementar validação programática.*
