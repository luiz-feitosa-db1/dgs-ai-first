Agora nós vamos para a última atividade. continue atuando com DM senior para me auxiliar.

O Tech Lead está montando o AGENTS.md do repositório e pediu que cada papel contribua com a seção que lhe diz respeito.

Recebemos os seguintes inputs:
- O cenário completo arquivo "cenario2-context.md"
- A estrutura do repositório (ver **Anexo C**).
- A estrutura do AGENTS.md proposta pelo Tech Lead:
  ```
  # AGENTS.md — NovaTech Assistant
  ## Project Overview
  ## Tech Stack & Architecture
  ## Coding Standards (Tech Lead)
  ## Product Rules & Guardrails (Product Specialist)
  ## Testing Standards (QA)
  ## Project Management Rules (Delivery Manager)
  ## Build & Deploy
  ```
- Validation gates simulados (output da atividade 1 "workflow-ai-first_novatech-assistente.md" — fornecidos para que este exercício seja autossuficiente, estou subindo novamente):
  ```
  Gate 1 — Spec → Plan: PS aprova requirements.md antes do TL gerar o plan.
  Gate 2 — Tasks → Implement: TL aprova tasks.md antes do Dev iniciar.
  Gate 3 — Code → Merge: TL faz code review; PR precisa de 1 approval.
  Gate 4 — Tests → Deploy: QA valida cobertura e cenários; TL aprova deploy.

Nossa tarefa é escrever a seção **"Project Management Rules"** do AGENTS.md. Esta seção será lida por agentes de IA quando gerarem artefatos de gestão, tasks, ou documentação. Ela deve conter:
1. Regras de nomenclatura de tasks e issues (ex: formato do título, labels obrigatórias).
2. Regras de documentação de decisões (ex: toda decisão técnica ou de escopo deve ser registrada como ADR em `/docs/adr/`).
3. Definição dos validation gates em formato consumível por agentes.
4. Restrições de comunicação que afetam geração de artefatos (ex: "documentos de status devem ser em português, código e comments em inglês").

Nossa entrega será a seção do AGENTS.md pronta para ser adicionada ao repositório.


Algumas restrições para nosso trabalho em conjunto:

1. Você não deve entregar tudo de uma vez ao final do prompt, ao invés disso, quero ir refinando o entendimento contigo até chegar em cada uma das versões finais de entrega.
2. Quero que você vá registrando as interações aqui pra manter um histórico do que formos conversando pra no final gerar um md com o histórico da conversa.