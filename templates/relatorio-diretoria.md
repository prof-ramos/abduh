---
template: relatorio-diretoria
version: 1.0.0
last_updated: 2026-06-17
---

# Template de Relatório para Diretoria — ASOF

---

## Estrutura

### Cabeçalho

```
RELATÓRIO ASOF Nº [NÚMERO]/[ANO]
Período: [data início] a [data fim]
[Local], [dia] de [mês] de [ano].
```

### Resumo Executivo

```
## Resumo Executivo

[3-5 linhas com os principais pontos do relatório]
```

### Desenvolvimento

```
## 1. [Tópico 1]

[Conteúdo do tópico 1]

## 2. [Tópico 2]

[Conteúdo do tópico 2]

## 3. [Tópico 3]

[Conteúdo do tópico 3]
```

### Conclusão

```
## Conclusão e Recomendações

[Resumo das conclusões e recomendações]
```

### Anexos (se aplicável)

```
## Anexos

- Anexo 1: [descrição]
- Anexo 2: [descrição]
```

---

## Campos Preenchíveis

| Campo | Exemplo | Obrigatório |
|---|---|---|
| `[NÚMERO]` | 003 | sim |
| `[ANO]` | 2026 | sim |
| `[data início]` | 01/01/2026 | sim |
| `[data fim]` | 30/06/2026 | sim |
| `[Local]` | Brasília | sim |
| `[dia]` | 17 | sim |
| `[mês]` | junho | sim |
| `[Resumo Executivo]` | 3-5 linhas | sim |
| `[Tópico N]` | Texto livre | sim (mín. 1) |
| `[Conclusão]` | Texto livre | sim |
| `[Anexos]` | Lista | condicional |

---

## Regras de Estilo

1. **Uso interno** da diretoria — linguagem objetiva, sem retórica
2. **Dados sensíveis** — sinalizar se o relatório contém dados que exigem restrição de acesso
3. **Aprovação formal** — sinalizar se o relatório requer aprovação da diretoria
4. **Anexos** — listar todos os documentos anexados com descrição clara

---

## Quality Gate

- [ ] Resumo executivo presente e conciso?
- [ ] Dados sensíveis sinalizados?
- [ ] Requer aprovação formal da diretoria?
- [ ] Anexos listados com descrição?
- [ ] Requer validação humana?
