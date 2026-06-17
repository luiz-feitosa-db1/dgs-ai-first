Aja como um Delivery Manager Senior e me ajude nas proximas fases do projeto da Novatech.

Nos arquivos, vc encontrará o arquivo "cenario2-context.md" com o contexto atual e as ultimas definições após a etapa de descoberta.

Nós teremos atividades para realizar juntos. 

A primeira delas, envolve definir como o time vai trabalhar no modelo AI First: quais ferramentas cada papel usa, qual o fluxo de trabalho, e quais são os checkpoints humanos (validation gates).

Nós temos os  seguintes inputs:
- O cenário completo que te enviei no "cenario2-context.md".
- Uma lista das ferramentas disponíveis no projeto:
  - GitHub Copilot (ativo para todos os devs e Tech Lead)
  - Claude (disponível para todo o time)
  - Claude Cowork (disponível para papéis não-dev)
  - Claude Design (disponível para Product Specialist)
  - Azure DevOps para boards e tracking
  - GitHub para repositório e CI/CD

Com base nisso nós precisamos:

1. Elaborar um fluxo de trabalho que mapeie, para cada papel do time, quais ferramentas de IA usa e em qual etapa do ciclo (Spec → Plan → Tasks → Implement → Review → Deploy).
2. Criar um template de checklist de validation gates — pontos onde um humano obrigatoriamente revisa e aprova antes de avançar. O checklist deve incluir ao menos:
   - Gate entre Spec e Plan (quem aprova a spec antes de gerar o plano?)
   - Gate entre Tasks geradas por IA e início de implementação (quem valida que as tasks fazem sentido?)
   - Gate entre código gerado por agente e merge (quem faz code review?)
   - Gate entre testes gerados por IA e deploy (quem valida que os testes são suficientes?)
3. Para cada gate, precisamos definir: quem aprova, o que verifica, quanto tempo tem, e o que acontece se reprovar.

Algumas restrições para nosso trabalho em conjunto:

1. Você não deve entregar tudo de uma vez ao final do prompt, ao invés disso, quero ir refinando o entendimento contigo até chegar em cada uma das versões finais de entrega.
2. Quero que você vá registrando as interações aqui pra manter um histórico do que formos conversando pra no final gerar um md com o histórico da conversa.