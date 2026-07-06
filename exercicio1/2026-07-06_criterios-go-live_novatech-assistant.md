# Critérios de Go-Live — NovaTech Assistant

**Data:** 2026-07-06
**Projeto:** NovaTech Assistant
**DM:** Luiz Eduardo
**Status do documento:** Rascunho — para apresentação na reunião de aprovação da diretoria NovaTech

> **Contexto de uso:** Este documento é apresentado na reunião com a diretoria da NovaTech. A autorização do go-live ocorre nessa reunião, após execução do checklist e apresentação dos indicadores. Não há go-live sem essa aprovação formal.

---

## Estratégia de Rollout

**Proposta: expansão faseada em duas ondas.**

| Onda | Escopo | Duração | Condição de avanço |
|---|---|---|---|
| Onda 1 | 5 atendentes-piloto (já em staging) | 1 semana em produção | Taxa de erro ≤ 5% e sem incidente crítico |
| Onda 2 | 45 atendentes (go-live total) | Após validação da Onda 1 | Indicadores da Onda 1 dentro dos critérios |

A Onda 1 não é um atraso — é a evidência que permite apresentar dados reais de produção na autorização da Onda 2. O risco de ir com 45 atendentes de uma vez é que qualquer problema afeta toda a operação simultaneamente.

---

## Critérios de Go-Live por Camada do Harness

### Camada 1 — Tool Orchestration

| Critério | Descrição | Tipo | Responsável |
|---|---|---|---|
| Query endpoint estável | POST `/query` responde com latência ≤ 5s em P95, sem erros 5xx em cenários de teste | Bloqueante | Tech Lead |
| Bot do Teams funcional | Bot recebe pergunta, encaminha para a API e retorna resposta formatada no canal sem falha de roteamento | Bloqueante | Tech Lead |
| Pipeline de ingestão executado | Os 847 documentos válidos estão indexados no Azure AI Search; data de última ingestão documentada | Bloqueante | Dev Sênior |
| Documentos contraditórios marcados no índice | Os 12 documentos com contradição pendente têm metadado `conflito: true` e data de vigência no índice (ADR-0003) | Bloqueante | Dev Sênior + NovaTech (Compliance) |
| Pipeline de ingestão incremental automático | Novos documentos publicados pela NovaTech são indexados em até 24h sem intervenção manual | Desejável | Dev Sênior |

---

### Camada 2 — Verification Loops

| Critério | Descrição | Tipo | Responsável |
|---|---|---|---|
| Taxa de erro ≤ 5% | Em conjunto de teste com ≥ 50 pares pergunta/resposta avaliados, respostas incorretas não ultrapassam 5% | Bloqueante | QA |
| Structured output validado | Toda resposta retorna os campos obrigatórios `answer`, `source_document` e `confidence_score`; respostas sem esses campos são rejeitadas programaticamente antes de chegar ao atendente | Bloqueante | Tech Lead |
| Cobertura dos domínios mais frequentes | Os tópicos prazos de entrega (35%), frete (25%) e devolução (20%) estão cobertos no conjunto de teste | Bloqueante | QA + PS |
| Fonte verificável | `source_document` retornado sempre corresponde a um documento existente no índice — sem alucinação de fonte | Bloqueante | QA |
| Confidence score calibrado | `confidence_score` correlaciona com acurácia real: respostas com score baixo têm taxa de erro maior que respostas com score alto | Desejável | Tech Lead + QA |

---

### Camada 3 — Context & Memory

| Critério | Descrição | Tipo | Responsável |
|---|---|---|---|
| Context budget respeitado | Nenhuma query ultrapassa o orçamento definido na ADR-0002 (~4K system prompt + ~8K chunks + pergunta + histórico de 3 turnos) | Bloqueante | Tech Lead |
| Histórico limitado a 3 turnos | Sessões com mais de 3 turnos truncam o histórico sem degradar a resposta do turno atual | Bloqueante | Dev |
| Prioridade por recência em documentos contraditórios | Quando chunks de versões diferentes são recuperados, o modelo prioriza o mais recente (ADR-0003) | Bloqueante | Dev |
| Isolamento de sessão | Informações de uma sessão do atendente A não vazam para a sessão do atendente B | Bloqueante | Tech Lead |
| Teste de degradação em sessões longas | Existe teste automatizado que valida qualidade da resposta na pergunta 4+ de uma mesma sessão (context rot) | Desejável | QA |

---

### Camada 4 — Guardrails

| Critério | Descrição | Tipo | Responsável |
|---|---|---|---|
| Guardrails de produto ativos | Sistema implementa os guardrails definidos pelo PS: nunca inventar prazos ou valores, sempre citar fonte, declarar "não encontrei" quando sem cobertura na base | Bloqueante | PS + Dev |
| HITL para respostas sobre carga perigosa (Onda 1) | Na Onda 1, toda resposta envolvendo carga perigosa (classes 1–6 ANTT) é sinalizada para revisão humana antes de chegar ao atendente, independentemente do confidence score | Bloqueante | Tech Lead + NovaTech |
| HITL para baixa confiança em temas críticos | Respostas com `confidence_score` abaixo do threshold definido (referência inicial: 0.6 — a calibrar com dados de teste) sobre SLA contratual e valores monetários passam por revisão humana antes de chegar ao atendente | Bloqueante | Tech Lead + NovaTech |
| Papel do revisor HITL nomeado | NovaTech designou quem revisa as respostas sinalizadas pelo HITL ⚠️ **Pendência: a ser definido com a diretoria nesta reunião** | Bloqueante | DM + NovaTech |
| Documentos contraditórios pendentes sinalizados ao atendente | Respostas baseadas em qualquer um dos 12 documentos com contradição não resolvida incluem aviso explícito ao atendente ⚠️ **Pendência: NovaTech não forneceu os tópicos cobertos por esses documentos — risco não dimensionado** | Bloqueante | Dev + NovaTech |
| Mecanismo de feedback do atendente | Atendente consegue sinalizar "resposta incorreta" diretamente no Teams, gerando registro rastreável | Bloqueante | Dev |
| Escalação explícita ao supervisor | Quando o assistente não encontra resposta na base, a mensagem ao atendente inclui instrução clara de escalar para o supervisor | Bloqueante | PS |

---

### Camada 5 — Observability

| Critério | Descrição | Tipo | Responsável |
|---|---|---|---|
| Logs de queries e respostas ativos | Toda query e toda resposta são logadas com timestamp, atendente (anonimizado), `confidence_score` e `source_document` | Bloqueante | Dev |
| Dashboard de taxa de erro em tempo real | Percentual de respostas incorretas (baseado em feedback dos atendentes) visível para o DM e para o responsável técnico da NovaTech | Bloqueante | Dev + DM |
| Alerta automático quando taxa de erro > 5% | Time de desenvolvimento é notificado automaticamente quando a taxa de erro nas últimas 24h ultrapassa 5% | Bloqueante | Dev |
| Rastreabilidade resposta → chunk → documento | Dada uma resposta específica, é possível identificar qual chunk foi recuperado e qual documento o originou | Bloqueante | Tech Lead |
| Dashboard de volume e latência | Volume de queries/dia e latência P50/P95 visíveis para acompanhamento operacional | Desejável | Dev |
| Relatório semanal automático | Relatório de uso e qualidade gerado automaticamente para o DM e para a NovaTech | Desejável | DM |

---

## Pendências que precisam ser resolvidas na reunião com a diretoria

Estas pendências são itens bloqueantes sem resolução interna possível — dependem de decisão ou informação da NovaTech.

| # | Pendência | Impacto se não resolvida |
|---|---|---|
| 1 | **Papel do revisor HITL:** Quem na NovaTech revisa respostas sinalizadas como baixa confiança ou envolvendo carga perigosa? ❓ **Dúvida em aberto:** o próprio atendente pode ser o revisor HITL — nesse caso, a resposta sinalizada chega a ele com um aviso explícito e ele decide conscientemente se a usa ou escala para o supervisor, sem necessidade de um papel dedicado de revisão. Precisamos alinhar com a NovaTech qual modelo operacional faz mais sentido. | O HITL existe no sistema mas não tem um humano responsável por executá-lo. Go-live da Onda 1 bloqueado. |
| 2 | **Tópicos dos 12 documentos contraditórios:** Sem saber quais temas esses documentos cobrem, não é possível dimensionar o risco. Algum deles pode cobrir SLA contratual ou carga perigosa. | Risco não dimensionado. Se algum documento crítico estiver nesse grupo sem resolução, a sinalização ao atendente pode ser insuficiente. |
| 3 | **Threshold do confidence score para HITL:** O valor de 0.6 é uma referência inicial. Precisa ser calibrado com dados reais de teste antes da Onda 1. | Threshold mal calibrado gera dois problemas: HITL excessivo (todos os atendimentos passam por revisão humana) ou HITL insuficiente (respostas incorretas passam sem revisão). |

---

## Plano de Rollback

### Onda 1 — 5 atendentes-piloto

**Triggers de rollback** (qualquer um é suficiente para acionar):
- Taxa de erro > 5% medida nos logs nas primeiras 48h após go-live
- Resposta incorreta entregue a um cliente final sobre carga perigosa, SLA contratual ou valor monetário
- Indisponibilidade do assistente por mais de 30 minutos consecutivos

**Quem decide:**
DM (Luiz Eduardo) em consenso com o ponto focal de operações da NovaTech. Se não houver consenso em 30 minutos, o DM decide e comunica formalmente.

**Ação de rollback:**
1. DM notifica o Tech Lead → bot do Teams desabilitado para os 5 atendentes-piloto (< 15 minutos)
2. Atendentes retornam ao processo manual de busca nas fontes originais (SharePoint, Confluence, pasta de rede)
3. DM comunica à NovaTech: motivo, prazo estimado de correção e próximo passo
4. Time realiza RCA (Root Cause Analysis) antes de qualquer nova tentativa de go-live

**Efeito sobre a Onda 2:**
Rollback na Onda 1 suspende automaticamente a autorização da Onda 2. A Onda 2 não é reagendada sem novo ciclo de testes e nova aprovação da diretoria.

### Onda 2 — 45 atendentes

**Triggers de rollback:** Os mesmos da Onda 1, com adição de:
- Degradação comprovada na qualidade do atendimento ao cliente final (reclamações formais acima do baseline pré-assistente)

**Quem decide:**
DM + Gerente de Operações da NovaTech (nível de impacto maior exige decisão conjunta formalizada).

**Ação de rollback:**
Mesmas etapas da Onda 1, com notificação adicional à diretoria e comunicação estruturada para os 45 atendentes sobre o retorno ao processo manual.

---

## Prompt para Claude Cowork — Dashboard de Readiness

```
Crie um dashboard de go-live readiness para o projeto NovaTech Assistant.

O dashboard deve ter:
- Título: "NovaTech Assistant — Go-Live Readiness" com campo de data de atualização
- Resumo executivo no topo com:
  - Total de critérios bloqueantes
  - Total de bloqueantes com status Pronto
  - Total de bloqueantes com status Pendente ou Em andamento
  - Semáforo geral: Verde se todos os bloqueantes estão prontos, Amarelo se algum está em andamento, Vermelho se algum não foi iniciado
  - Lista fixa das 3 pendências críticas (que dependem da NovaTech resolver)

- Uma seção por camada do harness, com tabela contendo as colunas:
  - Critério (nome)
  - Tipo (Bloqueante / Desejável)
  - Status (com semáforo visual: Verde = Pronto, Amarelo = Em andamento, Vermelho = Não iniciado)
  - Responsável
  - Data-alvo
  - Observações

As 5 camadas são:
  1. Tool Orchestration
  2. Verification Loops
  3. Context & Memory
  4. Guardrails
  5. Observability

Use os critérios exatamente como definidos no documento de go-live do NovaTech Assistant (DM: Luiz Eduardo, data 2026-07-06). Criar em formato editável que o time possa atualizar semanalmente. Os campos de Status, Responsável e Data-alvo devem estar em branco para preenchimento pelo time.
```
