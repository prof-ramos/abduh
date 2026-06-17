---
template: oficio
version: 1.0.0
last_updated: 2026-06-17
source: Ofício ASOF-015/2025 (LaTeX)
---

# Template de Ofício — ASOF

Estrutura baseada no Manual de Redação da Presidência da República e no padrão ASOF-015/2025.

---

## Estrutura

### 1. Cabeçalho

```
┌─────────────────────────────────────────────┐
│         [LOGO ASOF — Brasão]                │
│                                             │
│         OFÍCIO Nº ASOF-[NÚMERO]/[ANO]       │
│                                             │
│  [Local], [dia] de [mês] de [ano].          │
└─────────────────────────────────────────────┘
```

### 2. Destinatário

```
À Sua Excelência o(a) Senhor(a)
[Título] [Nome completo]
[Cargo]
[Órgão/Instituição]
[Endereço — Logradouro, número]
[CEP – Cidade/UF]
```

### 3. Assunto

```
Assunto: [título do assunto, conciso e completo].
```

### 4. Vocativo

```
Senhor(a) [cargo no feminino/masculino]
```

**Regra:** Não usar vírgula após o vocativo (conforme Manual de Redação da PR).

### 5. Corpo

Parágrafos numerados em sequência (`1.`, `2.`, `3.`...):

```
1. [Contexto — referência ao documento/norma que motiva o ofício].
2. [Desenvolvimento — exposição dos fatos, fundamentação, solicitação].
3. [Conclusão — fecho institucional, encaminhamento].
```

### 6. Fecho

```
Atenciosamente,

[assinatura]

[Nome completo do signatário]
[Cargo]
```

> **Nota:** Usar "Respeitosamente" para autoridades superiores (Ministro, Presidente).
> Usar "Atenciosamente" para demais autoridades (padrão).

---

## Campos Preenchíveis

| Campo | Exemplo | Obrigatório |
|---|---|---|
| `[NÚMERO]` | 015 | sim |
| `[ANO]` | 2026 | sim |
| `[Local]` | Brasília | sim |
| `[dia]` | 6 | sim |
| `[mês]` | agosto | sim |
| `[Título]` | Ministra de Primeira Classe | sim |
| `[Nome completo]` | Clarissa Souza Della Nina | sim |
| `[Cargo]` | Diretora do DSE | sim |
| `[Órgão]` | Ministério das Relações Exteriores | sim |
| `[Endereço]` | Palácio Itamaraty | sim |
| `[CEP]` | 70170-900 | sim |
| `[Cidade/UF]` | Brasília/DF | sim |
| `[Assunto]` | II Congresso Internacional... | sim |
| `[Vocativo cargo]` | presidente | sim |
| `[Nome signatário]` | Manuel Alves Bezerra | sim |
| `[Cargo signatário]` | Presidente da ASOF | sim |
| `[Parágrafos corpo]` | Texto livre | sim (mín. 1) |

---

## Variantes de Vocativo

| Gênero do destinatário | Vocativo correto |
|---|---|
| Masculino | `Senhor presidente` |
| Feminino | `Senhora presidente` |
| Masculino (plural) | `Senhores ministros` |
| Feminino (plural) | `Senhoras ministras` |

---

## Regras de Estilo (Vinculantes)

1. **Nunca inventar** número de ofício, data ou assinatura — usar dados reais fornecidos
2. **Numerar parágrafos** em ordem crescente (`1.`, `2.`, `3.`)
3. **Não usar vírgula** após o vocativo (`Senhora presidente` — sem vírgula)
4. **Não usar emojis** no corpo do ofício
5. **Não usar linguagem publicitária** ou adjetivos desnecessários
6. **Documento oficial exige validação humana** antes do envio
7. **LGPD:** Não incluir dados pessoais desnecessários de associados no corpo do ofício
8. **Referência cruzada:** Se o ofício responde outro documento, citar número e data: "Em resposta ao Ofício nº X/ANO, de [data]..."

---

## Exemplo Completo (baseado no Ofício ASOF-015/2025)

```
OFÍCIO Nº ASOF-015/2025

Brasília, 6 de agosto de 2025.

À Sua Excelência a Senhora
Ministra de Primeira Classe Clarissa Souza Della Nina
Diretora do Departamento de Serviço Exterior – DSE
Ministério das Relações Exteriores
Palácio Itamaraty
70170-900 – Brasília/DF

Assunto: II Congresso Internacional de Enfrentamento ao Assédio e à
Discriminação no Serviço Público – Ofício nº 5/2026/II Congresso.

Senhora presidente

1. Em resposta ao Ofício nº 5/2026/II Congresso, de 06 de março de
2026, por meio do qual V. Sa. solicita o apoio institucional e
financeiro da ASOF ao II Congresso Internacional de Enfrentamento ao
Assédio e à Discriminação no Serviço Público, manifesto o
reconhecimento desta Associação pela iniciativa e pelo relevante tema
abordado.

2. Não obstante o mérito do evento, informo que a ASOF, após análise
das possibilidades orçamentárias e das prioridades institucionais para
o exercício de 2026, não estará em condições de participar como
patrocinadora, correalizadora ou apoiadora do referido congresso.

3. Registro os votos de sucesso na realização do evento.

Atenciosamente,

Manuel Alves Bezerra
Presidente da ASOF
```

---

## Geração de PDF

O ofício original foi produzido em LaTeX. Para gerar PDF:

```latex
% Pré-requisitos: logo-asof.png no mesmo diretório
% Compilar com: pdflatex oficio.tex

\documentclass[12pt,a4paper]{article}
\usepackage[utf8]{inputenc}
\usepackage[brazil]{babel}
\usepackage{graphicx}
\usepackage{geometry}
\usepackage{fancyhdr}
\usepackage{parskip}
\usepackage{setspace}
\usepackage{ragged2e}
\geometry{top=2cm, bottom=2cm, left=3cm, right=1.5cm}
\setstretch{1.0}
\pagestyle{fancy}
\fancyhf{}
\fancyfoot[C]{\scriptsize\textbf{ASOF – Associação Nacional dos Oficiais de Chancelaria do Serviço Exterior Brasileiro} \\
Esplanada dos Ministérios, Bloco H – Zona Cívico-Administrativa – 1.º Subsolo, Sala ASOF \\
Brasília/DF – CEP 70170-900 \quad | \quad CNPJ: 26.989.392/0001-57}

\begin{document}

\begin{center}
    \includegraphics[width=0.25\textwidth]{logo-asof.png}
\end{center}

\vspace{0.5cm}
\noindent\textbf{OFÍCIO Nº ASOF-[NÚMERO]/[ANO]}

\vspace{0.3cm}
\begin{flushright}
[Local], [dia] de [mês] de [ano].
\end{flushright}

\vspace{0.5cm}
\noindent À Sua Excelência o(a) Senhor(a) \\
\textbf{[Título Nome]} \\
[Cargo] \\
[Órgão] \\
[Endereço] \\
[CEP] – [Cidade/UF]

\vspace{0.8cm}
\noindent\textbf{Assunto:} [Assunto completo].

\vspace{0.8cm}
Senhor(a) [cargo]

\begin{flushleft}
 1. [Parágrafo 1 — contexto].
 2. [Parágrafo 2 — desenvolvimento].
 3. [Parágrafo 3 — conclusão].
\end{flushleft}

\vspace{0.8cm}
Atenciosamente,

\vspace{1cm}
\begin{center}
\textbf{[Nome]} \\
[Cargo]
\end{center}

\end{document}
```

---

## Quality Gate para Ofícios

Antes de entregar um ofício, verificar:

- [ ] Número e data são dados reais (não inventados)?
- [ ] Destinatário tem nome, cargo e endereço completos?
- [ ] Assunto está conciso e completo?
- [ ] Vocativo está no gênero correto?
- [ ] Parágrafos numerados?
- [ ] Nenhum dado pessoal de associado exposto indevidamente?
- [ ] Documento exige validação humana antes do envio? (sempre sim para ofícios)
