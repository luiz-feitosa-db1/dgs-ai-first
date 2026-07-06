# Harness de Produto para Melhoria Contínua — NovaTech Assistant

**Data:** 2026-07-06
**Projeto:** NovaTech Assistant
**DM:** Luiz Eduardo
**Pré-requisitos:** `2026-07-06_guardrails-produto_novatech-assistant.md` (versão oficial de guardrails), Anexo B (mapa de cobertura), Anexo C (estrutura do repositório)

> **Objetivo:** garantir que o assistente evolui após o go-live sem degradar o que já funciona e sem violar os guardrails formalizados. Este documento cobre os 3 componentes do harness de produto: feedback, regression testing e HITL.

---

## 1. Processo de Feedback

### 1.1 Captura

O atendente sinaliza uma resposta problemática diretamente no Teams, via o adaptive card de feedback (já previsto em `src/bot/cards/feedback-card.ts` e na API `feedback-api`, ver Anexo C). O registro captura:

| Campo | Descrição |
|---|---|
| `query_original` | A pergunta feita pelo atendente |
| `resposta_recebida` | A resposta completa do assistente, incluindo `source_document` e `confidence_score` |
| `motivo` | Categoria fechada (ver 1.2) |
| `comentario_livre` | Campo opcional para contexto adicional |
| `atendente_id` | Anonimizado, para análise de padrão sem exposição individual |
| `timestamp` | Data e hora do atendimento |

### 1.2 Classificação (triagem semanal — PS + QA)

Todo feedback recebido é classificado em uma das categorias abaixo. A categoria determina o destino:

| Categoria | O que significa | Destino |
|---|---|---|
| **Gap de documentação** | O assistente não tinha a informação porque ela não existe na base indexada | Encaminhar à NovaTech (Gerente de Projetos) para fornecer o documento-fonte |
| **Documento desatualizado ou contraditório** | A informação existe, mas em versão errada ou em conflito não resolvido | Escalar ao Compliance da NovaTech via Gerente de Projetos; marcar como pendência ativa |
| **Falha de prompt** | O documento certo estava disponível, mas o assistente interpretou ou aplicou a regra errada (ex: inversão de regra, tom inadequado) | Vira issue técnica no Azure DevOps, ajuste em `prompts/system-prompt.md` |
| **Falha de retrieval** | O chunk errado foi recuperado (versão errada, documento irrelevante, ou nada foi recuperado quando deveria) | Vira issue técnica de pipeline (`chunker.ts`, `embedder.ts` ou configuração de índice) |
| **Falso negativo** | O assistente disse "não encontrei" quando a informação estava disponível e deveria ter sido entregue (caso Q4 dos guardrails) | Prioridade alta — mesma trilha de falha de retrieval, mas tratado como violação de guardrail, não apenas bug |
| **Violação de guardrail confirmada** | O feedback reproduz um dos guardrails formalizados sendo quebrado (N1, N2, N3, D1–D4, Q1–Q4) | Prioridade máxima — sempre gera um novo caso no baseline de regressão (ver seção 2) |

### 1.3 Da classificação à melhoria

1. Todo item de feedback vira um work item no Azure DevOps, com label da categoria e vínculo à query original.
2. SLA de triagem: até 48h úteis para classificar (volume esperado nas primeiras semanas pós-go-live é alto o suficiente para justificar cadência semanal de revisão, mas sem deixar acumular).
3. Todo item classificado como **violação de guardrail confirmada** ou **falso negativo** é automaticamente adicionado como novo caso em `prompts/eval/golden-queries.json` — o baseline de regressão cresce a cada falha real encontrada em produção. Isso é aprovado pelo QA (ver seção 3, HITL item 4).
4. Itens de **gap de documentação** e **documento desatualizado** não geram mudança técnica — geram uma pendência de conteúdo que só a NovaTech resolve. O assistente não pode "corrigir" a ausência de um documento; só pode ser corrigido depois que o documento existir e for indexado.
5. Mudanças de prompt ou pipeline seguem o fluxo de aprovação da seção 3 antes de ir a produção.

---

## 2. Regression Testing de Produto

### 2.1 Composição do baseline (`prompts/eval/golden-queries.json`)

O baseline nasce da combinação de duas fontes e cresce com o tempo via feedback (seção 1.3):

| Origem | O que cobre | Quantidade inicial |
|---|---|---|
| Mapa de cobertura do Anexo B | Testes de **retrieval**: dada uma pergunta, os chunks certos são recuperados? | 10 perguntas |
| Guardrails formalizados | Testes de **guardrail**: dada uma resposta (mesmo com retrieval correto), ela respeita a regra? | Mínimo 7 casos — um por guardrail com enforcement em código ou híbrido (D1, D2, D4, N1, N2, N3, Q1, Q3, Q4) |
| Os 3 incidentes originais | Reprodução exata dos casos que já falharam, para garantir que nunca mais regridam | 3 casos (I1, I2, I3) |

Cada caso no baseline tem: pergunta, chunks esperados (quando aplicável), guardrail testado (quando aplicável), resposta esperada ou critério de aprovação, e tipo de verificação (determinística ou por amostragem).

### 2.2 Dois tipos de verificação

**Determinística (código) — roda em todo PR, via CI:**
Cobre os guardrails com enforcement em código (D2, N2, N3, Q4 — ver documento de guardrails). São asserts binários: passou ou falhou. Qualquer falha aqui bloqueia o merge automaticamente.

**Por amostragem (avaliação qualitativa) — roda antes de cada deploy, não em todo PR:**
Cobre os guardrails probabilísticos ou híbridos (D1 correção de citação, D3, D4 menção do aviso, N1 completo, Q1 texto do aviso, Q3 decisão de conteúdo) e os testes de retrieval do Anexo B. Como a saída do LLM varia, a verificação compara a resposta atual contra a resposta da última rodada aprovada (não contra um "gabarito perfeito" fixo) — o critério é **não piorou**, não **é perfeito**. Revisão feita por QA, com apoio de um segundo LLM como avaliador (LLM-as-judge) para triagem inicial, seguida de revisão humana nos casos discordantes ou nos guardrails críticos (N2, I1, I2, I3).

### 2.3 Quando a suíte roda

| Gatilho | O que roda |
|---|---|
| Todo PR que altera `src/services/`, `src/pipeline/` ou `prompts/system-prompt.md` | Suíte determinística completa (CI, obrigatório) |
| Antes de qualquer deploy para produção | Suíte determinística + suíte por amostragem completa |
| Novo documento indexado que resolve ou introduz uma contradição | Suíte determinística + casos de guardrail D4/Q3 especificamente |
| Semanalmente, independente de mudança | Suíte por amostragem completa, para capturar drift (o pipeline de embeddings ou o próprio modelo Azure OpenAI pode mudar de comportamento sem que o time tenha alterado nada) |

### 2.4 Critério de bloqueio

- Qualquer falha na suíte determinística → bloqueia merge ou deploy, sem exceção.
- Queda na suíte por amostragem (uma resposta que antes estava correta e agora não está) → não bloqueia automaticamente, mas **bloqueia a aprovação humana do gate de deploy** (ver seção 3) até o Tech Lead e o PS decidirem se é uma regressão real ou uma variação aceitável.

---

## 3. Pontos de Human-in-the-Loop

Nem toda mudança tem o mesmo risco. O harness diferencia o que precisa de aprovação e de quem.

| # | Tipo de mudança | Quem aprova | Critério |
|---|---|---|---|
| H1 | Mudança em `prompts/system-prompt.md` | Tech Lead sempre; **+ Product Specialist** se a mudança afeta redação ou comportamento ligado a algum guardrail | Toda mudança de prompt é registrada em `prompts/prompt-changelog.md` (data, autor, motivo, resultado esperado) antes do merge |
| H2 | Novo documento indexado (adição normal, sem conflito) | Product Specialist | Valida que o documento não introduz ambiguidade não sinalizada |
| H3 | Documento que resolve uma contradição pendente (um dos 12 casos do Compliance) | Product Specialist **+ Gerente de Projetos NovaTech** | Envolve decisão de conteúdo que pertence ao cliente — a DGS não decide sozinha qual versão é a vigente |
| H4 | Novo caso adicionado a `golden-queries.json` (baseline cresce) | QA | Garante que o baseline não infla com casos mal formulados ou redundantes |
| H5 | Alteração de threshold (`confidence_score` mínimo, score mínimo de retrieval) | Tech Lead + QA | Mudança sistêmica que afeta todos os HITL de baixa confiança (Q1, Q2) definidos no go-live |
| H6 | Qualquer alteração que remova ou desative um teste determinístico de guardrail (D2, N2, N3, Q4) | Tech Lead **+ Product Specialist**, aprovação dupla obrigatória | Estes são os guardrails mais críticos do domínio (ligados a I1 e à segurança regulatória de carga perigosa); nunca devem ser suprimidos silenciosamente |
| H7 | Deploy para produção | Tech Lead aprova a suíte técnica; **DM confirma o deploy** (conforme papel já definido no time) | Gate final, formaliza a responsabilidade de scope/risco do DM |

### Observação sobre o papel da NovaTech

O Gerente de Projetos NovaTech só entra como aprovador quando a mudança envolve **conteúdo de documentação sensível** (H3) — resolução de contradição ou introdução de novo procedimento oficial. Mudanças puramente técnicas (prompt, threshold, pipeline) ficam na governança interna da DGS, para não sobrecarregar o cliente com decisões que não são dele.

---

## Conexão com o restante do harness (Exercício 3.1)

Este harness de produto opera **depois** do go-live definido no Exercício 3.1. Os pontos H3 e H6 reforçam duas pendências já sinalizadas naquele documento: a resolução dos 12 documentos contraditórios (H3) e a integridade do guardrail N2 sobre carga perigosa (H6, que é o mesmo guardrail por trás do HITL de carga perigosa da Onda 1). Não são processos isolados — o harness de go-live garante que o sistema começa confiável; este harness garante que ele continua confiável.
