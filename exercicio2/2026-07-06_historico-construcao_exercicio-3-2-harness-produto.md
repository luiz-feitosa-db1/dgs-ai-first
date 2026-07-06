# Histórico de Construção — Exercício 3.2: Harness de Produto para Melhoria Contínua

**Data:** 2026-07-06
**Projeto:** NovaTech Assistant
**Papel:** Delivery Manager (Luiz Eduardo) em par com o Manager Assistant
**Entregáveis relacionados:** `2026-07-06_guardrails-produto_novatech-assistant.md`, `2026-07-06_harness-produto-melhoria-continua_novatech-assistant.md`

---

## Objetivo do exercício

Projetar, do ponto de vista de produto, o harness que garante que o assistente melhore sem degradar após o go-live: processo de feedback, regression testing de produto (preservando os guardrails do cenário 2), e pontos de human-in-the-loop para aprovação de mudanças.

## Método de trabalho

Este exercício exigiu uma etapa extra em relação ao 3.1: antes de montar o harness, foi preciso resolver uma lacuna de insumo — os guardrails do cenário 2, citados no enunciado como pré-requisito, não estavam disponíveis em formato completo em nenhum documento do projeto.

---

## Linha do tempo da construção

### 1. Dúvidas de escopo levantadas antes de qualquer rascunho

1. **Conteúdo dos guardrails DEVE/NÃO DEVE/QUANDO EM DÚVIDA do cenário 2** — o exercício presumia que esse artefato já existia formalizado, mas não havia sido fornecido.
2. **Quem aprova mudanças no assistente** — só internamente na DB1, ou também a NovaTech?
3. **Existe um baseline de testes já definido** — os cenários de falha do QA (cenário 1) viraram um gabarito de pergunta/resposta esperada, ou ainda é uma lista sem gabarito?

### 2. Respostas do DM (primeira rodada)

| Pergunta | Resposta |
|---|---|
| Aprovação de mudanças | Gerente de Projetos NovaTech |
| Baseline de testes | Vamos criar |
| Guardrails | Não localizados de imediato pelo DM |

### 3. Investigação do insumo faltante — guardrails

O DM enviou o Anexo B (chunks de referência do RAG). O assistente reconheceu que esse anexo é útil para o mapa de cobertura de retrieval, mas não é o artefato de guardrails em si, e propôs reconstruir os guardrails do zero a partir das "armadilhas" do Anexo B, caso o artefato original não existisse.

O DM pediu para o assistente explicar o que esperava desse artefato antes de reconstruir — o assistente detalhou a estrutura DEVE / NÃO DEVE / QUANDO EM DÚVIDA com exemplos concretos do domínio.

Em seguida, o DM enviou o material completo do cenário 2 (Anexo A, Anexo C, Anexo D e o arquivo de exercícios do cenário 2), incluindo o texto integral do **Exercício 2.2** (definição de guardrails como artefato de produto), que trazia a estrutura oficial e os 3 incidentes de referência a serem usados na rastreabilidade.

O assistente então localizou, dentro do próprio arquivo de exercícios do cenário 2 (usado como input do Exercício 2.3), uma **versão resumida e já formalizada** dos guardrails — evitando reconstrução especulativa. Essa versão resumida cobria DEVE, NÃO DEVE e QUANDO EM DÚVIDA, mas sem classificação de enforcement (prompt vs. código) nem rastreabilidade explícita aos 3 incidentes, exigidas pelo Exercício 2.2.

### 4. Decisão de expandir os guardrails como pré-requisito

O assistente propôs expandir a versão resumida encontrada para o formato completo do Exercício 2.2, como etapa preparatória antes de montar o harness do 3.2. O DM confirmou.

**Resultado da expansão:**
- Cada guardrail da versão resumida foi conectado a pelo menos um dos 3 incidentes fornecidos (I1: inversão de regra sobre carga perigosa; I2: contradição de versões do PROC-042; I3: falso negativo sobre SLA Gold).
- Cada guardrail foi classificado como enforcement em **código** (determinístico), **prompt** (probabilístico) ou **híbrido**.
- O assistente identificou uma lacuna: os 3 incidentes originais cobriam apenas excesso de confiança (alucinação, citação errada) — nenhum guardrail formal prevenia o padrão oposto do Incidente 3 (recusa indevida quando a informação existia). Propôs um guardrail novo, **Q4**, para fechar essa lacuna, com enforcement 100% em código.

O DM validou o Q4 e definiu que este documento expandido passaria a ser a **versão oficial** dos guardrails do projeto, substituindo a versão resumida que circulava como input do AGENTS.md.

### 5. Confirmação da estrutura do baseline de regressão

Antes de escrever o harness, o assistente propôs a composição do baseline de testes de regressão:
- **Retrieval:** as 10 perguntas do mapa de cobertura do Anexo B.
- **Guardrail:** casos derivados dos guardrails com enforcement determinístico ou híbrido.
- **Incidentes:** os 3 casos originais reproduzidos como testes permanentes.

O DM confirmou a estrutura proposta sem alterações.

### 6. Geração do documento completo do harness

Com os guardrails oficiais e a estrutura do baseline fechados, o assistente produziu o harness cobrindo os 3 componentes pedidos:
1. **Processo de feedback** — captura no Teams, classificação em 6 categorias, SLA de triagem de 48h, e a regra de que toda violação de guardrail confirmada ou falso negativo vira automaticamente um novo caso no baseline.
2. **Regression testing** — separação entre verificação determinística (roda em todo PR, via CI) e verificação por amostragem (roda antes de cada deploy e semanalmente, com LLM-as-judge + revisão humana), com critério de bloqueio diferenciado para cada tipo.
3. **Pontos de HITL** — 7 tipos de mudança mapeados (H1 a H7), cada um com aprovador específico; a NovaTech (Gerente de Projetos) só entra como aprovador quando a mudança envolve conteúdo de documentação sensível (H3), não em mudanças puramente técnicas.

### 7. Observações de risco levantadas pelo assistente ao final

Sem que o DM tivesse pedido, o assistente sinalizou dois pontos de atenção sobre o próprio harness proposto:
- **H6 (proteção contra remoção de testes determinísticos de guardrail)** foi apontado como o item mais frágil do processo — fácil de contornar sob pressão de prazo (ex: a demo em 2 semanas), e o assistente sugeriu reforçar com proteção de CI/branch em vez de depender só de disciplina de processo.
- **A cadência semanal da suíte por amostragem** foi identificada como uma estimativa sem dado real de volume de produção, a ser recalibrada após a Onda 1 do go-live.

Esses pontos ficaram registrados como pendências de ajuste futuro, sem bloquear a entrega do documento.

---

## Decisões registradas (para referência rápida)

| Decisão | Justificativa |
|---|---|
| Guardrails resumidos do cenário 2 localizados e reaproveitados (não reconstruídos do zero) | Evita invenção de regras que já existiam formalmente em outro arquivo do projeto |
| Guardrail Q4 adicionado (bloqueio de falso negativo) | Os 3 incidentes originais não cobriam o padrão de falha do Incidente 3 (recusa indevida) |
| Documento expandido de guardrails vira versão oficial, substituindo a resumida | Elimina duas fontes de verdade divergentes sobre o mesmo artefato |
| Baseline de regressão combina retrieval (Anexo B) + guardrail + incidentes | Cobre tanto "a busca funciona?" quanto "a resposta final respeita a regra?" |
| NovaTech só aprova mudanças de conteúdo sensível (H3), não mudanças técnicas | Evita sobrecarregar o cliente com decisões que são de governança interna da DGS |

## Pontos em aberto ao final do exercício

- Avaliar se H6 deve virar proteção técnica de CI/branch, além de regra de processo.
- Recalibrar a cadência da suíte por amostragem com dados reais de volume pós-Onda 1.
- Atualizar o AGENTS.md do repositório para referenciar a versão oficial de guardrails em vez da versão resumida.
