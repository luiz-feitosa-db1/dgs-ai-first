# Histórico de Construção — Project Management Rules (AGENTS.md)
# NovaTech Assistente de IA

**Projeto:** NovaTech — Assistente de IA para Atendimento  
**Sessão:** DM + Manager Assistant (DB1 Global Software)  
**Data:** 2026-06-16  
**Artefato gerado:** `2026-06-16_agents-md-project-management-rules_novatech.md`

---

## Contexto da Sessão

Sessão de co-construção da seção `Project Management Rules` do `AGENTS.md` do repositório `novatech-assistant`. Esta seção é consumida por agentes de IA ao gerar artefatos de gestão, tasks e documentação — portanto as regras precisam ser prescritivas, sem margem para interpretação.

**Inputs utilizados:**
- Cenário 2 (context file)
- Anexo C — Estrutura do Repositório
- Workflow AI First + Validation Gates (Atividade 1)
- Governança de Specs SDD (Atividade 2)

---

## Interação 1 — Nomenclatura de tasks e issues: tipo de prefixo

**DM Assistant perguntou:** Prefixo por módulo do sistema ou por tipo de trabalho?

**DM respondeu:** Por tipo de trabalho.

**Decisão registrada:** Issues identificadas por tipo no título, não por módulo.

---

## Interação 2 — Definição dos tipos válidos

**DM Assistant propôs:** `[FEAT]`, `[BUG]`, `[TEST]`, `[INFRA]`, `[DOCS]`, `[REFACTOR]`

**DM corrigiu e expandiu:**
- `FEAT` → `NF` (nova funcionalidade)
- `BUG` → defeito encontrado pelo QA nos testes
- `BUG_CLIENT` → defeito encontrado pelo cliente em produção (garantia — sem cobrança)

**Decisões registradas:**
- Tipos finais: `NF` | `BUG` | `BUG_CLIENT` | `TEST` | `INFRA` | `DOCS` | `REFACTOR`
- `BUG_CLIENT` tem semântica de garantia — distingue responsabilidade comercial

---

## Interação 3 — Campos obrigatórios para BUG_CLIENT

**DM Assistant perguntou:** Issues do tipo `[BUG_CLIENT]` precisam de campos adicionais obrigatórios?

**DM respondeu:** Sim — número do chamado do cliente, sprint em que o código foi entregue, e responsável pela correção.

**Decisão registrada:** Template obrigatório para `[BUG_CLIENT]` com três campos: `client-ticket`, `delivery-sprint`, `assigned-to`. Issue sem esses campos não deve ser aceita.

---

## Interação 4 — Labels obrigatórias

**DM Assistant propôs:** Labels de módulo + labels de status de spec.

**DM Assistant perguntou:** Labels de módulo fazem sentido? Adicionar prioridade e sprint?

**DM respondeu:** Sim para todas. Sprint como label é boa ideia.

**Decisões registradas:**
- Labels obrigatórias na criação: módulo, prioridade, sprint
- Labels situacionais: `spec-approved`, `spec-change`
- Conjunto de módulos: `pipeline-ingestao`, `query-endpoint`, `feedback-api`, `teams-bot`, `painel-web`
- Prioridades: `high`, `medium`, `low`
- Sprint: `sprint-NN` (sequencial)

---

## Interação 5 — Critério para criação de ADRs

**DM Assistant propôs:** 5 critérios objetivos para gatilho de ADR — escolha de tecnologia, mudança major de spec, mudança em `/prompts/`, mudança no pipeline de ingestão, decisão que contradiz ADR existente.

**DM respondeu:** Critério completo para o projeto.

**DM Assistant perguntou:** Quem cria o ADR — sempre o TL ou depende do tipo?

**DM respondeu:** Sempre o TL, independente do tipo.

**Decisão registrada:** TL é responsável único pela criação de ADRs — remove ambiguidade para o agente.

---

## Interação 6 — Restrições de idioma por artefato

**DM Assistant propôs:** Tabela de idiomas por artefato — inglês para código, commits, ADRs e AGENTS.md; português para specs, status, issues e PRs.

**DM aprovou:** Sem ajustes.

**Decisão registrada:** Agente aplica idioma correto por tipo de artefato sem exceções.

---

## Interação 7 — Rascunho v0.1 da seção

**DM Assistant propôs:** Rascunho v0.1 completo com as 4 seções: nomenclatura, ADRs, validation gates e restrições de idioma.

**Dois pontos para validação:**
1. Gate 1 com três aprovadores (PS + TL + DM) — mais completo que o enunciado sugeria
2. AGENTS.md em inglês gerando artefatos em português — intencional?

**DM respondeu:**
1. Confirma os três aprovadores no Gate 1
2. Não gera problemas — comportamento intencional

**Decisão registrada:** Seção fechada sem ajustes adicionais.

---

## Interação 8 — Fechamento e geração dos documentos

**DM Assistant consolidou** a seção final v1.0 pronta para ser adicionada ao AGENTS.md.

**DM solicitou** geração de 2 arquivos:
- MD 1: Seção `Project Management Rules` pronta para o repositório
- MD 2: Histórico de construção (este arquivo)

---

## Resumo das Decisões

| # | Decisão | Origem |
|---|---|---|
| 1 | Prefixo de issues por tipo de trabalho, não por módulo | DM confirmou |
| 2 | Tipos válidos: NF, BUG, BUG_CLIENT, TEST, INFRA, DOCS, REFACTOR | DM definiu |
| 3 | BUG_CLIENT tem semântica de garantia — sem cobrança ao cliente | DM definiu |
| 4 | BUG_CLIENT exige 3 campos obrigatórios: client-ticket, delivery-sprint, assigned-to | DM confirmou |
| 5 | Labels obrigatórias na criação: módulo, prioridade, sprint | DM confirmou |
| 6 | Labels situacionais: spec-approved, spec-change | DM confirmou |
| 7 | ADR criado sempre pelo TL, independente do tipo de decisão | DM confirmou |
| 8 | 5 critérios objetivos definem quando criar ADR | DM confirmou |
| 9 | Gate 1 tem três aprovadores: PS + TL + DM | DM confirmou |
| 10 | Idioma por artefato: inglês para código/ADR/AGENTS.md; português para specs/issues/PRs | DM confirmou |

---

## Versões dos Rascunhos

| Artefato | Versão | Mudança |
|---|---|---|
| Project Management Rules | v0.1 | Proposta inicial — aprovada sem ajustes após validação de 2 pontos |
| Project Management Rules | v1.0 | Versão final entregue |
