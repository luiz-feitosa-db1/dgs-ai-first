# Governança de Specs — Spec Driven Development
# NovaTech Assistente de IA

**Projeto:** NovaTech — Assistente de IA para Atendimento  
**Elaborado por:** Delivery Manager — DB1 Global Software  
**Versão:** 1.0  
**Data:** 2026-06-16

---

## 1. Responsabilidades por Artefato

| Artefato | Autor | Revisor | Aprovador |
|---|---|---|---|
| `requirements.md` | Product Specialist | TL + QA | TL (viabilidade técnica) + DM (escopo e risco) |
| `plan.md` | Tech Lead | Dev Sênior + PS | PS (aderência ao requisito) |
| `tasks.md` | Dev com Copilot | TL | TL |

---

## 2. Localização no Repositório

Cada módulo tem sua própria pasta dentro de `/specs/`:

```
specs/
├── pipeline-ingestao/
│   ├── requirements.md
│   ├── plan.md
│   └── tasks.md
├── query-endpoint/
│   ├── requirements.md
│   ├── plan.md
│   └── tasks.md
├── feedback-api/
│   ├── requirements.md
│   ├── plan.md
│   └── tasks.md
├── teams-bot/
│   ├── requirements.md
│   ├── plan.md
│   └── tasks.md
└── painel-web/
    ├── requirements.md
    ├── plan.md
    └── tasks.md
```

Nenhum arquivo de spec vive fora da sua pasta de módulo. Sem exceções.

---

## 3. Nomenclatura e Versionamento

**Arquivos:** Nomes fixos — `requirements.md`, `plan.md`, `tasks.md`. O slug do módulo está na pasta, não no nome do arquivo.

**Cabeçalho obrigatório em todo arquivo de spec:**

```markdown
---
modulo: pipeline-ingestao
artefato: requirements
versao: 1.0
autor: [nome]
data: AAAA-MM-DD
---
```

**Critério semântico:**

- **Major (v1.0 → v2.0):** Mudança de escopo — novo requisito, remoção de requisito, alteração de critério de aceite existente. Retrocede o item no board e exige nova rodada de aprovação via PR.
- **Minor (v1.0 → v1.1):** Refinamento sem impacto de escopo — correção de ambiguidade, melhoria de clareza, adição de exemplo. Não retrocede o item no board. Exige registro no histórico do arquivo e notificação ao TL via comentário no PR.

**Histórico de mudanças:** Seção obrigatória ao final de todo arquivo de spec:

```markdown
## Histórico de Mudanças

| Versão | Data | Autor | Tipo | Descrição |
|---|---|---|---|---|
| 1.0 | 2026-06-16 | [nome] | Major | Versão inicial |
```

---

## 4. Fluxo de Aprovação via Pull Request

Cada transição entre artefatos exige um PR dedicado:

| Transição | Branch de origem | Aprovadores no PR |
|---|---|---|
| Criação do `requirements.md` | `spec/[modulo]/requirements` | TL + DM |
| Criação do `plan.md` | `spec/[modulo]/plan` | PS |
| Criação do `tasks.md` | `spec/[modulo]/tasks` | TL |

**Regras de PR para specs:**
- PRs de spec seguem os mesmos gates definidos no Workflow AI First (`2026-06-16_workflow-ai-first_novatech-assistente.md`)
- PR do `tasks.md` só pode ser aprovado com todos os IDs de issues do Azure DevOps preenchidos no arquivo
- Toda mudança de spec (major ou minor) abre um novo PR — nunca edição direta na branch principal

---

## 5. Rastreabilidade Specs → Azure DevOps

- Cada `tasks.md` aprovado gera issues no Azure DevOps
- Toda issue deve conter no campo de descrição o caminho do arquivo de origem: `specs/[modulo]/tasks.md`
- O `tasks.md` deve conter, para cada task, o ID da issue correspondente no DevOps:

```markdown
## Task 1 — Implementar extrator de PDF
**DevOps:** #1042
**Critério de aceite:** ...
```

- **Fonte de verdade do status de execução:** Azure DevOps
- **Fonte de verdade do escopo:** arquivo `tasks.md` no repositório

---

## 6. Board de Tracking

Status possíveis para cada artefato de spec:

| Status | Descrição |
|---|---|
| Rascunho | Artefato em escrita pelo autor responsável |
| Em Revisão | PR aberto, aguardando aprovação dos revisores |
| Aprovada | PR mergeado, artefato pronto para próxima etapa |
| Em Implementação | Tasks em execução no Azure DevOps |
| Validada | QA validou, critérios de aceite verificados |

**Estado inicial dos 5 módulos:**

| Módulo | requirements.md | plan.md | tasks.md |
|---|---|---|---|
| Pipeline de Ingestão | Rascunho | — | — |
| Query Endpoint | Rascunho | — | — |
| Feedback API | Rascunho | — | — |
| Bot do Teams | Rascunho | — | — |
| Painel Web | Rascunho | — | — |

> `—` indica que o artefato ainda não pode ser iniciado — depende da aprovação do artefato anterior.

---

## 7. Change Management

### 7.1 Gatilhos de Mudança

| Origem | Exemplo | Frequência esperada |
|---|---|---|
| Cliente (NovaTech) | Nova regra de negócio, mudança de requisito | Alta |
| Time técnico | Gap descoberto durante implementação | Média |

### 7.2 Classificação e Fluxo por Tipo

**Mudança Minor (v1.x → v1.x+1)**
1. Autor original abre PR com a correção
2. Notifica TL via comentário no PR
3. TL aprova — sem cerimônia adicional
4. Item no board não retrocede
5. Histórico de mudanças atualizado no arquivo

**Mudança Major (vX.0 → vX+1.0)**
1. DM classifica a mudança e comunica ao time
2. Item no board retrocede para **Em Revisão**
3. Autor original revisa o artefato afetado e abre PR
4. Fluxo de aprovação normal (mesmos aprovadores do artefato)
5. Se o `requirements.md` muda em major → `plan.md` e `tasks.md` do mesmo módulo precisam ser revisados
6. Issues no DevOps afetadas são atualizadas ou fechadas e reabertas
7. DM registra a mudança como ADR em `/docs/adr/` se houver impacto arquitetural

### 7.3 Regra de Impacto em Cascata

| Artefato alterado | Impacto obrigatório |
|---|---|
| `requirements.md` (major) | `plan.md` e `tasks.md` precisam ser revisados |
| `plan.md` (major) | `tasks.md` precisa ser revisado |
| `tasks.md` (major) | Issues no DevOps precisam ser atualizadas |
