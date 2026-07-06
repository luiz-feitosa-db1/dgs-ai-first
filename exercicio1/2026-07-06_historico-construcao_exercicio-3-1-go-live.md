# Histórico de Construção — Exercício 3.1: Critérios de Go-Live com Harness de Governança

**Data:** 2026-07-06
**Projeto:** NovaTech Assistant
**Papel:** Delivery Manager (Luiz Eduardo) em par com o Manager Assistant
**Entregável relacionado:** `2026-07-06_criterios-go-live_novatech-assistant.md`

---

## Objetivo do exercício

Definir os critérios que autorizam o go-live do assistente na demo para a diretoria da NovaTech, organizados pelas 5 camadas do harness (Tool Orchestration, Verification Loops, Context & Memory, Guardrails, Observability), com distinção bloqueante/desejável, ao menos um ponto de HITL, e um plano de rollback.

## Método de trabalho

O exercício não foi resolvido em uma única resposta. Antes de qualquer entrega, o assistente levantou dúvidas de escopo e só avançou após alinhamento explícito — seguindo o padrão de trabalho já estabelecido no projeto (calibrar perguntas → rascunho → validação incremental → documento final).

---

## Linha do tempo da construção

### 1. Dúvidas de escopo levantadas antes de qualquer rascunho

O assistente identificou 4 pontos que mudariam diretamente o conteúdo dos critérios:

1. **Demo vs. go-live são o mesmo evento?** — Necessário para saber se os critérios valem para um ambiente de apresentação ou para produção real com os 45 atendentes.
2. **Qual a taxa de erro aceitável para autorizar o go-live?** — Sem um número-alvo, o critério de Verification Loops ficaria vago (hoje o sistema tem 12% de erro em testes internos).
3. **Quem é o revisor humano no HITL?** — O ponto de HITL do exercício (carga perigosa / baixa confiança) precisava de um papel real, não hipotético.
4. **Os 12 documentos com contradição pendente do Compliance da NovaTech são bloqueantes?** — Risco potencial não dimensionado, dependendo de quais temas esses documentos cobrem.

### 2. Respostas do DM

| Pergunta | Resposta |
|---|---|
| Demo vs. go-live | São o mesmo evento — a demo inclui checklist e indicadores, e a diretoria autoriza ou não o go-live nessa reunião |
| Taxa de erro aceitável | ≤ 5% |
| Revisor HITL | Não existe ainda — fica como papel genérico, a definir com a diretoria na própria reunião |
| Documentos contraditórios | Sem informação sobre os tópicos cobertos — segue como pendência relevante sinalizada, não como bloqueio resolvido |

### 3. Pergunta de acompanhamento do assistente

Antes de escrever os critérios, o assistente perguntou se o go-live seria para os 45 atendentes de uma vez ou faseado a partir dos 5 pilotos já em staging — apontando que isso mudava diretamente o plano de rollback (impacto de desligar 5 pessoas vs. 45 em produção).

### 4. Recomendação proposta pelo assistente

Diante da pergunta "o que você acha que seria mais sensato?", o assistente propôs **rollout em duas ondas**:
- **Onda 1** — os 5 atendentes-piloto já em staging vão para produção por 1 semana.
- **Onda 2** — expansão para os 45, condicionada aos indicadores da Onda 1 dentro do critério de ≤ 5% de erro e sem incidente crítico.

Justificativa apresentada: a Onda 1 não é atraso, é a evidência que sustenta a autorização da Onda 2 com dados reais de produção, em vez de apenas dados de staging. O risco de ir direto com 45 atendentes foi apontado como concentração de impacto em caso de falha.

**Decisão do DM:** aprovada sem alterações.

### 5. Geração do documento completo

Com todas as variáveis fechadas, o assistente produziu o documento completo, incluindo:
- Estratégia de rollout em duas ondas
- Critérios por camada do harness (bloqueante/desejável)
- Pendências explícitas para levar à reunião com a diretoria (papel do revisor HITL, tópicos dos 12 documentos, calibração do threshold de confiança)
- Plano de rollback com triggers, responsável pela decisão e ação, separado por onda
- Prompt pronto para o Claude Cowork gerar o dashboard de readiness

### 6. Validação de origem de dado

O DM questionou de onde veio o número de 45 atendentes. O assistente indicou a fonte exata: o cenário do Exercício 1 (`exercicio-fase-1-entendimento.md`), que descreve a equipe de atendimento como 45 pessoas. Nenhuma alteração foi necessária — o dado estava correto e rastreável.

### 7. Duas iterações de refinamento

**Iteração 1 — Dúvida sobre o revisor HITL:**
O DM pediu para adicionar, na pendência do revisor HITL, a possibilidade de o próprio atendente ser o revisor (em vez de um papel dedicado). O assistente atualizou a Pendência 1 do documento com essa alternativa explícita, apresentando os dois modelos operacionais possíveis (atendente como revisor vs. revisor externo) para a diretoria decidir.

**Iteração 2 — Coluna de responsáveis ausente:**
O DM notou que faltava a informação de responsável por critério. O assistente propôs uma distribuição completa por papel (Tech Lead, Dev Sênior, QA, PS, DM, NovaTech) antes de editar, para validação prévia — só aplicou a mudança no documento depois da confirmação do DM.

---

## Decisões registradas (para referência rápida)

| Decisão | Justificativa |
|---|---|
| Rollout em duas ondas (5 → 45) | Reduz impacto de falha e gera evidência real de produção antes da expansão total |
| Taxa de erro ≤ 5% como critério bloqueante | Definido pelo DM como threshold de negócio |
| Revisor HITL como pendência em aberto, com duas alternativas apresentadas | Não há decisão operacional da NovaTech ainda; documento não assume um modelo sem validação |
| 12 documentos contraditórios tratados como bloqueante com risco não dimensionado | Falta de informação não pode virar suposição de segurança |
| Responsáveis atribuídos por papel em todas as 5 camadas | Necessário para o checklist ser executável, não apenas descritivo |

## Pontos em aberto ao final do exercício

- Confirmar com a diretoria quem exerce o papel de revisor HITL (atendente ou revisor dedicado).
- Obter da NovaTech os tópicos cobertos pelos 12 documentos contraditórios pendentes.
- Calibrar o threshold de confidence score (referência inicial de 0.6) com dados reais de teste.
