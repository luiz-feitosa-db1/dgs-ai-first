# Histórico de Construção — Governança de Specs SDD
# NovaTech Assistente de IA

**Projeto:** NovaTech — Assistente de IA para Atendimento  
**Sessão:** DM + Manager Assistant (DB1 Global Software)  
**Data:** 2026-06-16  
**Artefato gerado:** `2026-06-16_governanca-specs-sdd_novatech-assistente.md`

---

## Contexto da Sessão

Sessão de co-construção da governança de specs no modelo Spec Driven Development (SDD) para a fase de estruturação do projeto NovaTech (Cenário 2). O objetivo era definir como specs são criadas, aprovadas, versionadas e rastreadas — tratando specs como contratos executáveis, não documentos passivos.

**Inputs utilizados:**
- Cenário 2 (context file)
- Anexo C — Estrutura do Repositório
- Fluxo SDD simplificado (requirements → plan → tasks)
- Lista dos 5 módulos do projeto
- Workflow AI First e Validation Gates (artefato da Atividade 1)

---

## Interação 1 — Calibração de premissas iniciais

**DM Assistant perguntou:**
1. Versionamento de specs: Git puro ou controle explícito no documento?
2. Rastreabilidade specs → código: referência cruzada entre tasks.md e Azure DevOps?
3. Change management: mudanças vêm mais do cliente ou do time técnico?

**DM respondeu:**
1. Git + controle de versão explícito no documento
2. Sim, referência cruzada obrigatória
3. Duas origens: NovaTech trazendo novas regras (mais comum) e time descobrindo gaps durante implementação

**Decisões registradas:**
- Versionamento duplo: Git como histórico + cabeçalho com versão no arquivo
- Rastreabilidade bidirecional: tasks.md aponta para DevOps, DevOps aponta para tasks.md
- Change management deve cobrir os dois gatilhos com níveis de formalidade diferentes

---

## Interação 2 — Definição do padrão de versionamento

**DM Assistant perguntou:** Padrão semântico (v1.0, v1.1, v2.0) ou sequencial simples (v1, v2, v3)?

**DM respondeu:** Padrão semântico.

**DM Assistant propôs critério:**
- **Major:** Mudança de escopo — retrocede board, exige nova aprovação
- **Minor:** Refinamento sem impacto de escopo — não retrocede board, notifica TL

**DM aprovou:** Critério aceito sem ajustes.

---

## Interação 3 — Rascunho v0.1 da governança

**DM Assistant propôs:** Rascunho v0.1 com responsabilidades, localização no repositório, nomenclatura, versionamento e rastreabilidade.

**Dois pontos em aberto identificados:**
1. Campo `status` no cabeçalho do arquivo duplicaria o board — manter nos dois ou só no board?
2. Referência cruzada tasks.md ↔ DevOps seria manual — PR só aprovado com IDs preenchidos?

**DM respondeu:**
1. Status fica somente no board — remover do cabeçalho do arquivo
2. PR do tasks.md só aprovado com todos os IDs preenchidos — processo mais limpo

**Decisões registradas:**
- Cabeçalho do arquivo: módulo, artefato, versão, autor, data — sem campo status
- Gate de PR do tasks.md: IDs do DevOps são pré-requisito para aprovação

---

## Interação 4 — Mecanismo de aprovação

**DM Assistant perguntou:** Aprovação de specs via Pull Request ou por outro mecanismo (DevOps, e-mail, verbal)?

**DM respondeu:** Via Pull Request — aproveita a estrutura do GitHub para histórico de aprovações.

**Decisão registrada:** Cada transição entre artefatos exige PR dedicado. Aprovadores definidos por artefato. Mesmos gates do Workflow AI First se aplicam.

---

## Interação 5 — Rascunho v0.2 da governança

**DM Assistant propôs:** Rascunho v0.2 incorporando todas as decisões anteriores — cabeçalho sem status, fluxo de aprovação via PR com branches nomeadas, rastreabilidade bidirecional.

**DM aprovou:** Governança fechada.

---

## Interação 6 — Definição do formato do board

**DM Assistant perguntou:** Board no Azure DevOps (template de configuração) ou Markdown independente (pronto para uso imediato)?

**DM respondeu:** Markdown por agora — DevOps ainda não está configurado.

**Decisão registrada:** Board como tabela Markdown no documento de governança. Migração para DevOps em momento posterior.

---

## Interação 7 — Rascunho v0.1 do Change Management

**DM Assistant propôs:** Rascunho com gatilhos, fluxo por tipo de mudança (minor e major) e regra de impacto em cascata.

**Fluxo Minor:** PR direto, notificação ao TL, sem retrocesso no board.  
**Fluxo Major:** DM classifica, board retrocede para Em Revisão, fluxo de aprovação normal, cascata obrigatória nos artefatos dependentes, ADR se houver impacto arquitetural.

**DM aprovou:** Processo aceito sem ajustes.

---

## Interação 8 — Fechamento e geração dos documentos

**DM Assistant consolidou** o documento final v1.0 com governança + board + change management.

**DM solicitou** geração de 2 arquivos:
- MD 1: Documento final da governança
- MD 2: Histórico de construção (este arquivo)

---

## Resumo das Decisões

| # | Decisão | Origem |
|---|---|---|
| 1 | Versionamento duplo: Git + cabeçalho explícito no arquivo | DM confirmou |
| 2 | Padrão semântico: major para escopo, minor para refinamento | DM confirmou |
| 3 | Status das specs fica somente no board — fora do cabeçalho do arquivo | DM confirmou |
| 4 | PR do tasks.md só aprovado com IDs do DevOps preenchidos | DM confirmou |
| 5 | Aprovação de specs via Pull Request — histórico no GitHub | DM confirmou |
| 6 | Board em Markdown por agora — migração para DevOps posterior | DM confirmou |
| 7 | Mudança major retrocede board e dispara cascata nos artefatos dependentes | DM confirmou |
| 8 | DM registra mudança major com impacto arquitetural como ADR em /docs/adr/ | DM confirmou |

---

## Versões dos Rascunhos

| Artefato | Versão | Mudança |
|---|---|---|
| Governança de Specs | v0.1 | Proposta inicial — cabeçalho com status, rastreabilidade manual sem gate |
| Governança de Specs | v0.2 | Status removido do cabeçalho; PR do tasks.md exige IDs; aprovação via PR |
| Change Management | v0.1 | Proposta inicial — aprovada sem ajustes |
| Documento consolidado | v1.0 | Versão final entregue |
