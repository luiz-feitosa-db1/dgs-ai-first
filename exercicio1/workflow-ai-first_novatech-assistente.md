# Workflow AI First — NovaTech Assistente de IA

**Projeto:** NovaTech — Assistente de IA para Atendimento  
**Elaborado por:** Delivery Manager — DB1 Global Software  
**Versão:** 1.0  
**Data:** 2026-06-16

---

## 1. Visão Geral do Modelo de Trabalho

O time opera em dois fluxos distintos e sequenciais:

**Fluxo Kanban (pré-sprint):** Spec → Plan. Assíncrono, sem timebox de sprint. Conduzido por PS e TL, com participação do QA desde o Refinamento e revisão do DM no Pre Planning. Um item só entra na sprint quando o Plan está aprovado (DoR).

**Fluxo Sprint (2 semanas):** Tasks → Implement → Review → Deploy. Inicia segunda-feira com Plan aprovado. Deploy ocorre na sexta-feira entre 14h e 17h.

---

## 2. Fluxo de Ferramentas por Papel e Etapa

### 2.1 Fluxo Kanban (pré-sprint)

| Etapa | Papel | Ferramentas de IA | Atividade |
|---|---|---|---|
| Refinamento | PS + TL + QA | Claude (chat) | Explorar escopo, levantar dependências, identificar riscos técnicos e mapear cenários de teste antecipados |
| Spec (requirements.md) | PS | Claude (chat) + Claude Cowork | Redigir requirements.md; usar Claude para estruturar critérios de aceite e identificar gaps; Cowork para artefatos visuais de apoio |
| Pre Planning (SP1) | PS + TL + DM + QA + time | Claude (chat) | PS usa Claude para preparar apresentação da spec; DM usa Claude para formular perguntas de validação e riscos; QA usa Claude para validar cobertura de cenários |
| Planning (SP2) | TL + Devs + QA | GitHub Copilot + Claude (chat) | TL usa Claude para gerar plan.md a partir da spec aprovada; Devs usam Copilot para decompor tasks; QA usa Claude para estruturar plano de testes |

### 2.2 Fluxo Sprint (2 semanas)

| Etapa | Papel | Ferramentas de IA | Atividade |
|---|---|---|---|
| Tasks | TL + Devs | GitHub Copilot + Claude (chat) | Refinar tasks geradas; identificar dependências técnicas |
| Implement | Devs | GitHub Copilot | Geração e completação de código; testes unitários assistidos |
| Review | TL + QA | GitHub Copilot + Claude (chat) | Code review assistido; QA usa Claude para revisar cobertura de cenários de teste |
| Deploy | TL + DM | Claude (chat) | DM usa Claude para gerar release notes e comunicado de deploy |

---

## 3. Validation Gates

### Gate 1 — Spec → Plan (Pre Planning / SP1)

| Campo | Definição |
|---|---|
| Quem aprova | PS (autor), TL (viabilidade técnica), DM (escopo e risco) |
| O que verifica | requirements.md tem critérios de aceite verificáveis para cada requisito? Os requisitos são implementáveis dentro de uma sprint? Há ambiguidades que travam o TL na geração do plan? QA consegue derivar cenários de teste a partir da spec? |
| Tempo disponível | Dentro da cerimônia de SP1 — timebox de 2 horas. |
| Se reprovar | Spec volta para o PS com comentários registrados no Azure DevOps. Nova rodada de SP1 agendada. Item não entra na Planning até aprovação. |

### Gate 2 — Tasks → Implement (início da sprint)

| Campo | Definição |
|---|---|
| Quem aprova | TL |
| O que verifica | As tasks geradas cobrem todo o escopo do plan.md? Cada task é atômica e executável por um Dev em no máximo 1 dia? As dependências entre tasks estão mapeadas e sequenciadas? As tasks de teste estão incluídas (não só as de implementação)? |
| Tempo disponível | Até o fim do primeiro dia da sprint (segunda-feira). |
| Se reprovar | Tasks são reescritas pelo Dev com apoio do Copilot e resubmetidas ao TL no mesmo dia. Sprint não inicia implementação até aprovação. |

### Gate 3 — Code → Merge (contínuo durante a sprint)

| Campo | Definição |
|---|---|
| Quem aprova | TL (obrigatório) + 1 Dev par (recomendado) |
| O que verifica | O código implementa o que a task especifica? Testes unitários estão presentes e passando? Não há regressões nos testes existentes? O código segue os padrões definidos no AGENTS.md? Código gerado por agente foi revisado linha a linha pelo Dev responsável? |
| Tempo disponível | SLA de revisão de PR: até 4 horas úteis após abertura. Sem limite de PRs simultâneos. |
| Se reprovar | PR volta com comentários. Dev corrige e reabre. Regressões repetidas (mais de 2 iterações no mesmo PR) são escaladas ao DM. |

### Gate 4 — Tests → Deploy (sexta-feira)

| Campo | Definição |
|---|---|
| Quem aprova | QA (cobertura e cenários) + TL (autorização técnica de deploy) + DM (confirmação de escopo entregue) |
| O que verifica | Todos os cenários de teste mapeados na SP1 foram executados? Cobertura de testes automatizados está em no mínimo 90%? Não há bugs críticos ou bloqueadores em aberto? O ambiente de staging está estável? O DM confirma que o escopo comprometido com o cliente foi entregue? |
| Tempo disponível | Até as 14h da sexta-feira. Deploy acontece entre 14h e 17h. |
| Se reprovar | Deploy é bloqueado. DM comunica ao cliente. Time faz triagem: corrige ainda na sexta se viável, ou carrega para a próxima sprint com post-mortem registrado no Azure DevOps. |
