# Avaliação de Riscos — IA Generativa no Projeto NovaTech
**Projeto:** Assistente de Atendimento com RAG — NovaTech × DB1 Global Software  
**Data:** 2026-06-06  
**Elaborado por:** Delivery Manager — DB1 Global Software  
**Versão:** 1.0  

---

## Contexto

A NovaTech contratou a DB1 para construir um assistente de IA que permita ao time de atendimento (45 pessoas, ~320 chamados/dia) consultar documentação interna em linguagem natural e receber respostas fundamentadas, com citação de fonte. A base documental é composta por ~1.250 fontes (SharePoint, Confluence, pasta de rede) gerenciadas por três áreas diferentes, sem processo unificado de revisão.

Este documento avalia os riscos técnicos e de negócio relacionados às características da IA generativa, com foco nos que podem impactar prazo, custo e qualidade do projeto.

---

## Riscos Identificados

---

### RISCO 01 — Alucinação sobre procedimentos inexistentes
**Probabilidade:** Alta  
**Impacto:** Alto (qualidade da entrega, reputação da solução)

LLMs geram respostas estatisticamente plausíveis, não necessariamente verdadeiras. Quando o pipeline de RAG recupera chunks insuficientes ou irrelevantes — ou quando a pergunta do atendente cobre um gap documental —, o modelo tende a "completar" a resposta com inferências não documentadas.

**Exemplo concreto no contexto da NovaTech:** A base não possui documento formal sobre frete padrão (abaixo de 500kg). Se um atendente perguntar "qual o prazo para entrega de 300kg para Salvador?", o modelo pode inventar um valor baseado nos multiplicadores da PROC-042, sem indicar que não há cobertura documental para esse cenário.

**Mitigação:**
- Instruir explicitamente o modelo, via system prompt, a responder "Não encontrei esta informação na documentação disponível" quando os chunks recuperados não cobrirem a pergunta — e nunca usar conhecimento geral para preencher lacunas.
- Implementar testes de regressão com perguntas sem cobertura documental como parte do ciclo de QA.
- Monitorar respostas sem citação de fonte como proxy de risco de alucinação.

---

### RISCO 02 — Documentação contraditória gerando respostas híbridas incorretas
**Probabilidade:** Alta (já identificada na base atual)  
**Impacto:** Alto (confiabilidade operacional, risco contratual)

A base da NovaTech já contém contradições conhecidas: PROC-042 v1 e v2 coexistem no SharePoint sem hierarquia clara, com multiplicadores regionais diferentes (ex: Norte: 1.6 vs. 1.8), fatores de peso diferentes (1.2 vs. 1.15 para 1.001–3.000kg) e prazos adicionais diferentes (+2 vs. +3 dias úteis). Se o pipeline recuperar chunks de ambas as versões na mesma query, o modelo pode misturar os valores sem sinalizar o conflito — gerando cotações erradas passadas ao cliente como corretas.

**Mitigação:**
- Implementar metadados de vigência obrigatórios no pipeline de ingestão: cada chunk deve carregar versão, data de emissão e status (`ativo` / `depreciado`).
- Adicionar instrução no system prompt para que, diante de chunks contraditórios, o assistente sinalize explicitamente o conflito e indique qual versão é mais recente.
- Incluir no processo de onboarding documental da NovaTech uma etapa de curadoria prévia: documentos sem vigência definida não devem ser indexados sem triagem humana.

---

### RISCO 03 — Degradação de qualidade por context rot em sessões longas
**Probabilidade:** Média  
**Impacto:** Médio (consistência da experiência do atendente)

Modelos LLM têm janela de contexto limitada. Em sessões de atendimento no Teams onde o atendente faz múltiplas perguntas sequenciais, o histórico da conversa consome tokens progressivamente. A partir de um determinado volume, informações fornecidas no início da sessão — incluindo instruções do system prompt e chunks críticos — começam a ser "esquecidas" ou subponderadas pelo modelo (efeito *lost in the middle*). O atendente pode receber respostas degradadas sem perceber.

**Mitigação:**
- Definir um orçamento de contexto por query: histórico de conversa não deve ultrapassar N tokens (a definir na ADR de arquitetura). Sessões longas devem ser truncadas ou resumidas automaticamente.
- Posicionar informações críticas (guardrails, instruções de prioridade de versão) no início e no fim do contexto, não no meio.
- Implementar monitoramento de tamanho de contexto por sessão como alerta operacional.

---

### RISCO 04 — Gap entre expectativa da diretoria e capacidade real da tecnologia
**Probabilidade:** Alta  
**Impacto:** Alto (satisfação do cliente, risco de rescisão ou retrabalho pós-go-live)

A diretoria da NovaTech expressou expectativa de que "o assistente vai saber tudo" e não precisará de busca manual. Esta expectativa é incompatível com o funcionamento real de sistemas RAG: a qualidade da resposta é limitada pela qualidade e cobertura da documentação-fonte, e o modelo não elimina — apenas reduz — a necessidade de julgamento humano. Gaps documentais, perguntas ambíguas e situações de exceção continuarão exigindo escalação para supervisores.

**Mitigação:**
- Realizar alinhamento formal de expectativas antes do kickoff, com apresentação de critérios de sucesso mensuráveis (ex: redução do tempo médio de busca de 12min para <2min; % de chamados respondidos sem escalação).
- Incluir no contrato ou no termo de aceite uma definição clara do escopo: o assistente responde com base na documentação indexada — perguntas fora do escopo são redirecionadas, não respondidas com conteúdo fabricado.
- Propor uma demo controlada com casos de uso reais antes do go-live para calibrar expectativas com dados concretos.

---

### RISCO 05 — Dependência da qualidade e atualização dos documentos-fonte
**Probabilidade:** Alta  
**Impacto:** Alto (qualidade estrutural da solução ao longo do tempo)

O assistente é tão bom quanto a documentação que o alimenta. A NovaTech atualiza sua base mensalmente, por três áreas distintas, sem processo unificado de revisão. Isso significa que documentos desatualizados, duplicados ou informais (como o FAQ-Atendimento, explicitamente não validado por Compliance) podem ser indexados com o mesmo peso que documentos normativos — e o modelo não tem como distinguir a confiabilidade das fontes sem metadados explícitos.

**Mitigação:**
- Classificar cada fonte por nível de confiabilidade no pipeline de ingestão (ex: `normativo`, `procedimental`, `informal`). Chunks de fontes informais devem ter instrução de uso mais restritivo no contexto.
- Estabelecer um SLA de atualização do índice: novos documentos publicados devem ser indexados em até 24h (requisito já levantado pelo Product Specialist).
- Criar um processo de curadoria contínua: documentos sem responsável ou sem data de revisão definida devem ser marcados como `pendente de validação` e não indexados automaticamente.
- Mapear, ainda na fase de discovery, todos os documentos com status ambíguo (ex: PROC-042 v1 sem marcação de obsolescência) e definir com a NovaTech qual versão é vigente antes da ingestão.

---

## Resumo Executivo de Riscos

| # | Risco | Probabilidade | Impacto | Prioridade |
|---|-------|--------------|---------|------------|
| 01 | Alucinação sobre procedimentos inexistentes | Alta | Alto | Crítica |
| 02 | Documentação contraditória gerando respostas híbridas | Alta | Alto | Crítica |
| 03 | Context rot em sessões longas | Média | Médio | Alta |
| 04 | Gap expectativa da diretoria vs. capacidade real | Alta | Alto | Crítica |
| 05 | Dependência da qualidade dos documentos-fonte | Alta | Alto | Crítica |

---

## Perguntas para o Tech Lead antes de confirmar o cronograma de 3 meses

1. **Qualidade da extração documental:** Dado que ~15% dos documentos do SharePoint são PDFs escaneados (OCR necessário) e a wiki do Confluence usa macros customizadas, qual é a estimativa realista de esforço para o pipeline de ingestão — e esse esforço está refletido no cronograma atual ou foi subestimado?

2. **Estratégia para documentos contraditórios:** O pipeline de RAG prevê algum mecanismo de detecção e sinalização de chunks conflitantes antes de enviá-los ao LLM, ou a resolução de contradições será delegada inteiramente ao model prompt? Qual é o risco de falso negativo (conflito não detectado)?

3. **Orçamento de contexto e estratégia de chunking:** Considerando ~12M de tokens na base estimada e uma janela de contexto de 128K tokens no GPT-4o, quantos chunks por query são viáveis sem degradar a qualidade? A estratégia de chunking atual foi validada com perguntas multi-domínio (ex: um atendente pergunta sobre SLA + regra de frete + política de devolução na mesma query)?

---

*Documento elaborado para uso interno — DB1 Global Software. Não compartilhar com o cliente sem revisão do DM responsável.*
