## Project Management Rules

> This section governs how AI agents must behave when generating management
> artifacts, tasks, issues, and documentation for the NovaTech Assistant project.
> Rules are prescriptive — follow them exactly, without interpretation.

---

### 1. Issue and Task Naming

#### 1.1 Title format
Every issue title must follow this pattern:

```
[TYPE] Short description in imperative form
```

Examples:
- `[NF] Implement PDF extractor in ingestion pipeline`
- `[BUG] Fix null pointer in query handler`
- `[BUG_CLIENT] Fix incorrect SLA displayed for Gold tier`
- `[TEST] Add unit tests for chunk overlap logic`
- `[INFRA] Configure Azure AI Search index`
- `[DOCS] Write requirements.md for feedback-api`
- `[REFACTOR] Extract prompt builder into standalone service`

Valid types: `NF` | `BUG` | `BUG_CLIENT` | `TEST` | `INFRA` | `DOCS` | `REFACTOR`

Type definitions:
- `NF` — new feature
- `BUG` — defect found by QA during testing
- `BUG_CLIENT` — defect found by client in production (warranty — no charge applies)
- `TEST` — creation or adjustment of tests
- `INFRA` — infrastructure and configuration
- `DOCS` — documentation and specs
- `REFACTOR` — refactoring without behavior change

#### 1.2 Mandatory labels (must be set at issue creation)
- **Module:** `pipeline-ingestao` | `query-endpoint` | `feedback-api` | `teams-bot` | `painel-web`
- **Priority:** `high` | `medium` | `low`
- **Sprint:** `sprint-01` | `sprint-02` | `sprint-NN`

#### 1.3 Situational labels (set when applicable)
- `spec-approved` — issue originates from an approved `tasks.md`
- `spec-change` — issue was affected by an in-progress spec change

#### 1.4 BUG_CLIENT additional fields (mandatory)
Issues of type `[BUG_CLIENT]` must include the following fields in the description:

```
---
client-ticket: [número do chamado NovaTech]
delivery-sprint: sprint-NN
assigned-to: [nome do desenvolvedor responsável]
---
[Descrição do bug e passos para reprodução]
```

All three fields are required. A `[BUG_CLIENT]` issue without these fields must not be accepted.

#### 1.5 Spec traceability
Every issue generated from a `tasks.md` must include in the description:

```
source-spec: specs/[module]/tasks.md
```

---

### 2. Decision Documentation (ADRs)

#### 2.1 When to create an ADR
An ADR must be created when a decision meets at least one of the following criteria:

- Technology or library choice affecting more than one module
- Spec change classified as major (version bump vX.0 → vX+1.0)
- Any change to files under `/prompts/` (system prompt changes are architectural decisions)
- Any change affecting the ingestion pipeline or chunking strategy in `/src/pipeline/`
- Any decision that contradicts an existing ADR

#### 2.2 Who creates ADRs
Always the Tech Lead, regardless of the decision type or origin.

#### 2.3 File naming and location
- Path: `/docs/adr/`
- Pattern: `NNNN-short-title-in-kebab-case.md`
- Example: `0001-azure-openai-model-selection.md`

#### 2.4 ADR format
Use the template at `/docs/adr/template.md`. Required sections:

```
# ADR-NNNN: [Title]
## Status: Proposed | Accepted | Deprecated
## Context
## Decision
## Consequences
## Alternatives Considered
```

---

### 3. Validation Gates

Agents must not advance work past a gate without explicit human approval.
Gates are sequential and non-skippable.

#### Gate 1 — Spec → Plan
- **Trigger:** `requirements.md` is ready for review
- **Approvers:** PS (authorship) + TL (technical feasibility) + DM (scope and risk)
- **Timebox:** 2 hours (SP1 ceremony)
- **Approval mechanism:** Pull Request on branch `spec/[module]/requirements`
- **On reject:** spec returns to PS with comments registered in Azure DevOps; new SP1 scheduled; item does not advance to Planning until approved

#### Gate 2 — Tasks → Implement
- **Trigger:** `tasks.md` is ready for review
- **Approver:** TL
- **Timebox:** end of sprint day 1 (Monday)
- **Approval mechanism:** Pull Request on branch `spec/[module]/tasks`
- **Prerequisite:** all Azure DevOps issue IDs must be filled in `tasks.md` before PR approval
- **On reject:** tasks rewritten by Dev with Copilot and resubmitted to TL same day; implementation does not start until approved

#### Gate 3 — Code → Merge
- **Trigger:** Pull Request opened by Dev
- **Approvers:** TL (mandatory) + 1 peer Dev (recommended)
- **SLA:** up to 4 business hours after PR is opened
- **Volume:** no limit on simultaneous open PRs
- **On reject:** PR returned with comments; Dev corrects and reopens; more than 2 iterations on the same PR must be escalated to DM

#### Gate 4 — Tests → Deploy
- **Trigger:** end of sprint (Friday)
- **Approvers:** QA (coverage and scenarios) + TL (technical sign-off) + DM (scope confirmation)
- **Deadline:** approval by 14:00 on Friday; deploy window 14:00–17:00
- **Minimum test coverage:** 90%
- **On reject:** deploy is blocked; DM communicates to client; team triages — fix same day if viable, otherwise carry to next sprint with post-mortem registered in Azure DevOps

---

### 4. Language and Communication Constraints

Agents must apply the correct language per artifact type.
No exceptions.

| Artifact | Language |
|---|---|
| Source code and comments | English |
| Git commits | English |
| ADRs | English |
| `AGENTS.md` | English |
| Specs (`requirements.md`, `plan.md`, `tasks.md`) | Portuguese |
| Status documents and client communications | Portuguese |
| Azure DevOps issue titles and descriptions | Portuguese |
| Pull Request descriptions | Portuguese |
