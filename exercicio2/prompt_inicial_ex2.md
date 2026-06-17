Agora vamos para a segunda atividade. mantenha todo o histórico que você tem e continue atuando como DM Senior junto comigo.

Definimos que o time vai usar Spec Driven Development. Specs não são documentos passivos — são contratos executáveis. Nós precisamos definir como specs são criadas, aprovadas, versionadas e rastreadas.

Tivemos os seguintes inputs:
- O cenário completo (arquivo de context que eu já te mandei).
- A estrutura do repositório do projeto (ver **Anexo C**) — as specs devem seguir a organização de diretórios definida.
- O fluxo SDD simplificado: *"requirements.md define o que precisa ser feito. plan.md define como será feito. tasks.md decompõe em unidades atômicas executáveis por agentes. Cada transição (requirements → plan → tasks) é um checkpoint humano."*
- Uma lista dos módulos do projeto que precisarão de specs:
  1. Pipeline de ingestão de documentos
  2. API de busca (query endpoint)
  3. API de feedback (atendente reporta resposta incorreta)
  4. Bot do Teams (interface conversacional)
  5. Painel web (dashboard de métricas e histórico)


Sendo assim precisamos:
**Tarefa:**
1. Definir um processo de governança de specs que cubra: quem cria cada tipo de spec (requirements pelo Product Specialist, plan pelo Tech Lead, tasks pelo Dev com apoio do Copilot), como as specs são nomeadas e versionadas, onde ficam no repositório, e como mudanças são rastreadas.
2. Criar um board de tracking (template de kanban ou tabela) que permita acompanhar o status de cada spec: Rascunho → Em Revisão → Aprovada → Em Implementação → Validada. Inclua os 5 módulos como itens iniciais.
3. Definir o que acontece quando uma spec precisa mudar depois de já estar em implementação (change management).

A saída final deverá conter

1. O documento de governança
2. Board de tracking gerado pelo Cowork
3. Processo de change management.


Algumas restrições para nosso trabalho em conjunto:

1. Você não deve entregar tudo de uma vez ao final do prompt, ao invés disso, quero ir refinando o entendimento contigo até chegar em cada uma das versões finais de entrega.
2. Quero que você vá registrando as interações aqui pra manter um histórico do que formos conversando pra no final gerar um md com o histórico da conversa.