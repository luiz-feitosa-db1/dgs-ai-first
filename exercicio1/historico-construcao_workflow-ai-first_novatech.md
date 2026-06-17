# Histórico de Construção — Workflow AI First NovaTech

**Projeto:** NovaTech — Assistente de IA para Atendimento  
**Sessão:** DM + Manager Assistant (DB1 Global Software)  
**Data:** 2026-06-16  
**Artefato gerado:** `2026-06-16_workflow-ai-first_novatech-assistente.md`

---

## Contexto da Sessão

Sessão de co-construção do fluxo de trabalho AI First para a fase de estruturação do projeto NovaTech (Cenário 2). O objetivo era definir quais ferramentas cada papel usa em cada etapa do ciclo de desenvolvimento, e quais são os checkpoints humanos obrigatórios (validation gates) antes de cada avanço.

---

## Interação 1 — Calibração de premissas iniciais

**DM Assistant perguntou:**
- Nível de familiaridade do time com ferramentas de IA
- Duração das sprints e dia de deploy
- Status do Azure DevOps

**DM respondeu:**
- Todo o time tem familiaridade com IA
- Sprints de 2 semanas, segunda a sexta. Deploy na sexta-feira
- Azure DevOps ainda não configurado — será definido junto com o fluxo

**Decisão registrada:** Azure DevOps será estruturado em paralelo com a definição do fluxo.

---

## Interação 2 — Entendimento do ciclo Spec → Deploy

**DM Assistant perguntou:** O ciclo completo acontece dentro de uma sprint ou existe preparação anterior?

**DM respondeu:** Spec e Plan acontecem fora da sprint, em fluxo Kanban separado. A sprint inicia quando o Plan está pronto — Plan é parte da DoR.

**Decisão registrada:** Dois fluxos distintos — Kanban (pré-sprint) e Sprint (2 semanas).

---

## Interação 3 — Entendimento das cerimônias do fluxo Kanban

**DM Assistant perguntou:** Quem inicia a Spec e qual é o papel do DM na escrita?

**DM respondeu:**
- Existe uma cerimônia de **Refinamento** (PS + TL) antes da escrita da Spec
- PS escreve a Spec após o Refinamento
- A Spec é validada no **Pre Planning (SP1)** com PS + TL + DM + time
- A estimativa ocorre na **Planning (SP2)**
- DM não participa da escrita — apenas da revisão no SP1

**Decisão registrada:** Fluxo Kanban mapeado em 4 etapas: Refinamento → Spec → SP1 → SP2.

---

## Interação 4 — Rascunho v0.1 do fluxo de ferramentas

**DM Assistant propôs:** Fluxo v0.1 com ferramentas por papel e etapa.

**Problema identificado:** QA estava inserido apenas no SP2. DM sinalizou que QA participa desde o Refinamento.

**Correção aplicada:** QA incluído no Refinamento e no SP1 — entra cedo para mapear cenários de teste durante o alinhamento de escopo.

---

## Interação 5 — Rascunho v0.2 do fluxo de ferramentas

**DM Assistant propôs:** Fluxo v0.2 com QA desde o Refinamento.

**DM Assistant perguntou:** Claude Cowork tem uso relevante para o papel do DM em alguma etapa?

**DM respondeu:** Não.

**Decisão registrada:** Claude Cowork removido do papel do DM. DM usa Claude (chat) nas etapas relevantes.

**DM aprovou:** Fluxo v0.2 fechado.

---

## Interação 6 — Rascunho v0.1 dos Validation Gates

**DM Assistant propôs:** 4 gates com estrutura: quem aprova / o que verifica / tempo disponível / se reprovar.

**Dois pontos em aberto identificados:**
1. Timebox do SP1 não definido
2. Percentual mínimo de cobertura de testes não definido

**DM respondeu:**
1. SP1 tem timebox de 2 horas
2. Cobertura mínima de 90%

**Decisão registrada:** Gates atualizados com os valores confirmados.

---

## Interação 7 — Validação do Gate 3

**DM Assistant perguntou:** O Gate 3 (Code → Merge) acontece de forma contínua durante a sprint. Há limite de PRs abertos simultaneamente por Dev?

**DM respondeu:** Não há limites. TL revisa conforme chegam os PRs, sem restrições de volume.

**Decisão registrada:** Gate 3 sem política de limite de PRs. SLA de revisão mantido em 4 horas úteis.

---

## Interação 8 — Fechamento e geração dos documentos

**DM Assistant consolidou** o documento final v1.0 com fluxo de ferramentas + validation gates.

**DM solicitou** geração de 2 arquivos:
- MD 1: Entrega final do documento
- MD 2: Walkthrough da construção (este arquivo)

---

## Resumo das Decisões

| # | Decisão | Origem |
|---|---|---|
| 1 | Azure DevOps será estruturado junto com o fluxo | DM confirmou |
| 2 | Dois fluxos distintos: Kanban (pré-sprint) e Sprint (2 semanas) | DM confirmou |
| 3 | Fluxo Kanban: Refinamento → Spec → SP1 → SP2 | DM confirmou |
| 4 | QA participa desde o Refinamento | DM corrigiu rascunho v0.1 |
| 5 | Claude Cowork não tem uso relevante para o DM | DM confirmou |
| 6 | Timebox do SP1: 2 horas | DM confirmou |
| 7 | Cobertura mínima de testes: 90% | DM confirmou |
| 8 | Gate 3 sem limite de PRs simultâneos | DM confirmou |

---

## Versões dos Rascunhos

| Artefato | Versão | Mudança |
|---|---|---|
| Fluxo de ferramentas | v0.1 | Proposta inicial — QA apenas no SP2 |
| Fluxo de ferramentas | v0.2 | QA incluído desde o Refinamento; Cowork removido do DM |
| Validation Gates | v0.1 | Proposta inicial — timebox SP1 e cobertura em aberto |
| Validation Gates | v0.2 | SP1 = 2h; cobertura mínima = 90%; Gate 3 sem limite de PRs |
| Documento consolidado | v1.0 | Versão final entregue |
