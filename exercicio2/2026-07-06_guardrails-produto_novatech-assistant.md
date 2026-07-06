# Guardrails de Produto — NovaTech Assistant

**Data:** 2026-07-06
**Projeto:** NovaTech Assistant
**Papel de origem:** Product Specialist (formalizado no Cenário 2, Exercício 2.2)
**Uso:** Artefato consumido por humanos (time) e por agentes de IA (prompt, harness, testes)

> **Status:** Versão oficial. Substitui a versão resumida que circulava no repositório como input do AGENTS.md (`## Product Rules & Guardrails`). O AGENTS.md deve ser atualizado para referenciar este documento como fonte, mantendo no arquivo apenas o resumo prescritivo necessário para os agentes de código — a rastreabilidade a incidentes e a classificação de enforcement completas vivem aqui.

---

## Os 3 incidentes de referência

| # | Incidente | Categoria de falha |
|---|---|---|
| I1 | O assistente respondeu que o prazo de devolução para carga perigosa é 7 dias, quando cargas perigosas NÃO podem ser devolvidas | Inversão de regra / alucinação de regra |
| I2 | O assistente citou "PROC-042, seção 2" mas usou multiplicadores da v1 (desatualizada), não da v2 (vigente) | Contradição de documentos não resolvida corretamente |
| I3 | O assistente disse "Não encontrei informação" para uma pergunta sobre SLA Gold, mas o SLA-2024 estava indexado e continha a resposta | Falso negativo / recusa inadequada |

---

## Legenda de Enforcement

| Tipo | Significado | Como se verifica |
|---|---|---|
| **Código** | Determinístico. Regra fixa aplicada por `response-validator.ts` ou schema, antes da resposta sair. Sempre consistente. | Teste automatizado, pass/fail binário |
| **Prompt** | Probabilístico. Depende do LLM seguir a instrução do system prompt. Não é garantido. | Avaliação por amostragem (golden queries + revisão) |
| **Híbrido** | Código detecta a condição (determinístico); o conteúdo gerado em resposta à condição depende do LLM ou é template fixo. | Combinação: teste automatizado para o trigger + amostragem para o conteúdo |

---

## DEVE (comportamentos obrigatórios)

| # | Guardrail | Enforcement | Rastreável a |
|---|---|---|---|
| D1 | Citar fonte com identificador do documento e seção em toda resposta | Híbrido — a *presença* do campo é validável por código (schema); a *correção* da citação (documento certo, seção certa) depende do LLM e é probabilística | I2 |
| D2 | Incluir campo `source_document` no JSON de retorno, mesmo com confiança baixa | Código — Zod valida o schema de resposta antes do envio; resposta sem o campo é rejeitada programaticamente | I3 |
| D3 | Responder em português formal | Prompt — tom e formalidade não são verificáveis deterministicamente; detecção de idioma (código) pode pegar o caso extremo de resposta em outro idioma, mas "formal" é qualitativo | — (guardrail geral, não ligado a um incidente específico) |
| D4 | Sinalizar explicitamente quando a resposta usa documento com contradição pendente (metadado `conflito: true`) | Híbrido — o pipeline marca o metadado no índice (código, ADR-0003); a menção do aviso na resposta depende do LLM seguir a instrução (prompt) | I2 |

---

## NÃO DEVE (comportamentos proibidos)

| # | Guardrail | Enforcement | Rastreável a |
|---|---|---|---|
| N1 | Gerar valores numéricos (prazos, multiplicadores, SLAs) que não estejam literalmente na documentação indexada | Híbrido — o prompt instrui a não inventar (probabilístico); mas é possível um checador determinístico pós-geração que extrai números da resposta e verifica se aparecem em algum chunk recuperado (código, camada de verificação adicional) | I2 (multiplicador da versão errada é um caso deste guardrail: o valor existe, mas não é o vigente) |
| N2 | Afirmar que carga perigosa (classes 1–6 ANTT) pode ser devolvida pelo processo padrão | **Código** — este é o guardrail mais crítico do domínio e admite regra fixa: se a pergunta contém termos de carga perigosa + devolução, a resposta é validada contra um padrão de negativa obrigatória antes de sair (mesmo critério do VC-03 do plano de testes do QA) | **I1** — incidente que motivou este guardrail |
| N3 | Inventar tiers de cliente (só existem Gold, Silver, Standard) | **Código** — lista fechada de tiers válidos; `response-validator.ts` rejeita qualquer resposta que mencione um tier fora dessa lista | Não está nos 3 incidentes formais, mas é o caso "Platinum" do Anexo B — incluído porque é o mesmo padrão de falha (alucinação de categoria inexistente) |

---

## QUANDO EM DÚVIDA (comportamentos de fallback)

| # | Guardrail | Enforcement | Rastreável a |
|---|---|---|---|
| Q1 | Prefixar resposta com aviso de baixa confiança quando `confidence_score` está abaixo do threshold | Híbrido — o trigger (score abaixo do limite) é checado por código; o texto do aviso pode ser template fixo inserido programaticamente (recomendado, torna esta parte também determinística) | I3 (relacionado à calibração de confiança) |
| Q2 | Sugerir escalação ao supervisor quando não há chunk relevante recuperado | Código, se implementado como template fixo disparado pela ausência de chunks acima do score mínimo | I3 |
| Q3 | Quando duas versões de um documento são recuperadas, priorizar a mais recente e informar que existe versão anterior | Híbrido — o metadado de vigência é código (ADR-0003); a decisão de qual conteúdo usar na resposta final depende do LLM interpretar essa instrução (prompt) | I2 |
| Q4 *(novo — proposto para cobrir I3)* | **NÃO** responder "não encontrei informação" quando há chunk recuperado com score de similaridade acima do threshold de retrieval | Código — se `chunks_recuperados.length > 0` e o score máximo excede o mínimo de relevância, uma resposta de "não encontrado" é sinalizada como anomalia e bloqueada antes de chegar ao atendente, disparando revisão humana | **I3** — incidente que motivou este guardrail (era o único gap descoberto: os guardrails originais previnem alucinação, mas nenhum previne o erro oposto — recusa indevida) |

---

## Observação sobre cobertura

Os 3 incidentes originais eram majoritariamente de **excesso de confiança** (alucinação, citação errada). O Q4 foi adicionado porque o Incidente 3 é o oposto: **falta de confiança indevida** — o sistema tinha a informação e recusou. Um harness que só testa contra alucinação deixaria esse tipo de regressão passar despercebido. Isso é relevante para o desenho do regression testing no Exercício 3.2: os golden queries precisam incluir casos de "resposta existe e deve ser entregue", não só casos de "resposta não deve ser inventada".

---

## Guardrails com enforcement 100% em código (candidatos a teste determinístico direto)

Para referência rápida do que pode virar assert automatizado sem depender de avaliação por amostragem:

- D2 — presença do campo `source_document`
- N2 — negativa obrigatória em pergunta de carga perigosa + devolução
- N3 — tier fora da lista fechada
- Q4 — bloqueio de "não encontrado" quando há chunk relevante acima do threshold

Os demais (D1 correção de citação, D3, D4 menção do aviso, N1 completo, Q1 texto do aviso se não for template fixo, Q3 decisão de conteúdo) dependem de avaliação por amostragem porque o conteúdo gerado é probabilístico.
