# Plano de Discovery — Assistente de IA NovaTech

**Projeto:** Assistente de IA para Atendimento ao Cliente  
**Cliente:** NovaTech  
**Elaborado por:** DB1 Global Software (DGS)  
**Data:** 2026-06-06  
**Versão:** 1.0

---

## Visão Geral

O discovery está dividido em duas fases sequenciais: **Intent** (agentes de IA pré-analisam a base documental) e **Discovery Humano** (validação, priorização e decisões que IA não faz). A Intent alimenta o Discovery — sem ela, o time humano chega às entrevistas às cegas.

---

## Fase 1 — Intent (Agentes de IA)

**Duração estimada:** 3–4 dias úteis, rodando em paralelo à mobilização do time humano.

### O que os agentes fazem

#### 1.1 Catalogação e inventário da base documental
- Varrer o SharePoint (~800 documentos), Confluence (~400 páginas) e pasta de rede (~50 planilhas)
- Extrair metadados: título, área responsável, data de criação, data de última modificação, formato, tamanho
- Classificar por domínio temático (frete, devolução, SLA, segurança de carga, compliance, etc.)
- Identificar documentos sem metadados de vigência ou sem responsável declarado

#### 1.2 Detecção de contradições e duplicidades
- Cruzar documentos com mesmo tema para identificar versões conflitantes (ex: dois procedimentos de frete com parâmetros diferentes, sem indicação de qual prevalece)
- Sinalizar documentos com numeração similar ou títulos sobrepostos
- Marcar pares ou grupos candidatos à análise humana de curadoria

#### 1.3 Mapeamento de frequência e cobertura temática
- Identificar quais temas aparecem com maior densidade na base (volume de documentos por assunto)
- Mapear temas com cobertura rasa (poucos documentos, possivelmente um gap real)
- Cruzar os temas mapeados com as categorias de chamados de atendimento (prazos, frete, devolução, reclamações) para estimar relevância operacional

#### 1.4 Análise de qualidade documental
- Sinalizar documentos escaneados sem OCR (não indexáveis para RAG)
- Identificar planilhas com fórmulas interdependentes que exigem tratamento especial
- Detectar páginas Confluence com links quebrados ou macros customizadas que podem impedir extração correta
- Estimar percentual da base que está "pronto para ingestão" vs. que precisa de pré-processamento

#### 1.5 Geração do mapa de gaps e dependências
- Produzir um relatório estruturado com: temas cobertos, temas com cobertura conflitante, temas ausentes
- Listar os 10–15 documentos mais críticos para o caso de uso de atendimento (candidatos à ingestão prioritária)
- Identificar dependências entre documentos (ex: POL-001 referencia PROC-088 — ambos precisam estar na base)

---

## Fase 2 — Discovery Humano

**Duração estimada:** 5–6 dias úteis, iniciando após entrega do relatório da Intent.

### Por que os humanos são insubstituíveis aqui

IA cataloga, detecta padrões e aponta candidatos. Mas quem decide qual versão de um documento prevalece, qual gap é crítico para o negócio, e o que o atendente realmente precisa — é humano. O Discovery existe para transformar o mapa da Intent em decisões.

### Atividades e responsáveis

#### 2.1 Validação do mapa de gaps `PS + Tech Lead` — 1 dia
- Revisar o relatório da Intent junto com alguém de Operações da NovaTech
- Para cada contradição sinalizada: confirmar se é real, definir qual versão é vigente, ou escalar para a área responsável
- Para cada gap: confirmar se é ausência real ou se o documento existe em outra fonte não mapeada
- **Resultado:** mapa de gaps validado, com prioridade e responsável por resolução

#### 2.2 Entrevistas com atendentes `PS + DM` — 2 dias
- Focar em 6–8 atendentes experientes (não todos os 45)
- Perguntas-chave:
  - Quais documentos você consulta mais?
  - Onde você mais perde tempo?
  - Quando você escala para o supervisor, qual foi a dúvida?
  - Já respondeu errado porque encontrou duas versões contraditórias?
- Coletar exemplos reais de perguntas de clientes que geraram dificuldade
- **Resultado:** lista priorizada de casos de uso reais, com frequência e criticidade

#### 2.3 Entrevistas com supervisores e gestores `DM` — 1 dia
- Quais os chamados mais críticos do ponto de vista de impacto ao cliente?
- Quais erros de informação já geraram reclamação formal ou perda de cliente?
- Qual o processo atual de atualização documental? Quem avisa o atendimento quando muda uma regra?
- **Resultado:** critérios de priorização validados pelo negócio, não apenas pela IA

#### 2.4 Workshop de curadoria com áreas responsáveis `PS + Tech Lead` — 1 dia
- Convidar representantes de Operações, Compliance e Comercial (as 3 áreas que atualizam docs)
- Pautar: contradições sinalizadas pela Intent que precisam de decisão humana
- Definir: processo de versionamento e indicação de vigência para novos documentos
- **Resultado:** decisões de curadoria registradas + acordo sobre processo de atualização futura

#### 2.5 Priorização e escopo do MVP `DM + PS` — 1 dia
- Com base em tudo que foi coletado, definir:
  - Quais documentos entram no MVP (não toda a base de 1.250 fontes)
  - Quais casos de uso o assistente deve cobrir no go-live
  - O que fica para iterações futuras
- Validar com a diretoria da NovaTech os critérios de sucesso mensuráveis
- **Resultado:** escopo do MVP documentado e aceito pelo cliente

---

## Sequência e Dependências

```
Dia 0–1    Acesso às fontes concedido pela NovaTech → agentes iniciam Intent
Dia 1–4    Intent roda em paralelo à mobilização e agendamento das entrevistas
Dia 4      Entrega do relatório da Intent ao time
Dia 5      Validação do mapa de gaps com NovaTech (2.1)
Dia 6–7    Entrevistas com atendentes (2.2) — usa o mapa de gaps como guia
Dia 8      Entrevistas com supervisores (2.3)
Dia 9      Workshop de curadoria (2.4) — usa contradições identificadas pela Intent
Dia 10     Priorização do MVP (2.5) — sintetiza tudo
```

> A Intent não é opcional nem pode acontecer depois. Sem ela, as entrevistas humanas chegam sem estrutura — o time pergunta "quais documentos você usa?" sem saber quais sequer existem na base.

---

## O que a NovaTech precisa fornecer

| O que | Quando | Para quê |
|---|---|---|
| Acesso de leitura ao SharePoint | Dia 0 | Agentes de Intent |
| Acesso de leitura ao Confluence | Dia 0 | Agentes de Intent |
| Acesso à pasta de rede (planilhas) | Dia 0 | Agentes de Intent |
| Lista dos 45 atendentes com tempo de casa e especialidade | Dia 3 | Seleção dos 6–8 para entrevista |
| Disponibilidade de 1 representante de cada área (Ops, Compliance, Comercial) | Dia 8 | Workshop de curadoria |
| Dados de volume de chamados por categoria | Dia 3 | Cruzamento com mapa temático |
| Pessoa de referência para decisões de curadoria documental | Dia 0 | Ponto de contato para contradições |

---

## Risco Principal a Monitorar

A Intent vai encontrar contradições que ninguém na NovaTech sabe resolver — porque nenhuma área se sente "dona" do problema. O workshop de curadoria (2.4) é o momento de forçar essa decisão.

Se a NovaTech não tiver um processo de governança documental, o RAG vai degradar em 3 meses mesmo que o MVP funcione bem. Isso precisa entrar no escopo de entrega como **requisito não-funcional**, não como "nice to have".
