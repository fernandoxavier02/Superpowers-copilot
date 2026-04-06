---
title: Pipeline-orchestrator canonical baseline
area: pipeline-orchestrator-baseline
tags:
  - pipeline-orchestrator
  - claude
  - windsurf
  - github-copilot
  - mirroring
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Congelar a baseline canonica do sistema `pipeline-orchestrator` antes de qualquer adaptacao para GitHub Copilot.

Este arquivo descreve o comportamento original que o mirror precisa preservar. Ele nao descreve ainda como o Copilot implementara cada capability.

## Escopo da baseline

Esta baseline cobre:

- porta de entrada obrigatoria via `ORCHESTRATOR_DECISION`;
- classificacao de tipo, persona, severidade e complexidade;
- cadeia operacional observada no workspace;
- outputs contratuais por etapa quando evidenciados;
- enforcement real versus guidance textual;
- superficies de comando realmente evidenciadas.

Ela nao cobre ainda:

- packaging final no Copilot;
- strategy de sync workspace -> global;
- harness especifico do mirror Copilot.

## Fontes canonicas minimas

### Semantic source of truth

- `.claude/INDEX.md`
- `.claude/ORCHESTRATOR_ENFORCEMENT.md`
- `.claude/hooks/force-pipeline-agents.cjs`
- `.claude/hooks/agent-context-injector.cjs`
- `.claude/hooks/__tests__/t01-ssot-uniqueness.test.cjs`

### Functional reference used for observed contracts

- `.windsurf/CONSTITUTION.md`
- `.windsurf/rules/00-core.md`
- `.windsurf/skills/orchestrate-task/SKILL.md`
- `.windsurf/workflows/ask.md`
- `.windsurf/workflows/enforce.md`
- `.windsurf/agents/README.md`
- `.windsurf/agents/context-classifier.md`
- `.windsurf/agents/orchestrator-documenter.md`
- `.windsurf/agents/executor-implementer.md`
- `.windsurf/agents/adversarial-reviewer.md`
- `.windsurf/agents/sanity-checker.md`
- `.windsurf/agents/final-validator.md`
- `.windsurf/slash-commands/COMMANDS-INDEX.md`

## Resolved authority choice for this mirror

Existe duplicidade real no repositorio para o shape do `ORCHESTRATOR_DECISION`.

Para este mirror, a autoridade semantica adotada e:

1. `.windsurf/CONSTITUTION.md`
2. `.windsurf/skills/orchestrate-task/SKILL.md`

Referencias com drift conhecido, nao usadas como SSOT:

- `.windsurf/agents/task-orchestrator.md` - output shape divergente, com `skill_detected` e sem o conjunto completo de 12 campos.
- `.windsurf/rules/00-core.md` - resumo operacional derivado.

## ORCHESTRATOR_DECISION canonico

O shape semantico canonico observado no workspace tem 12 campos:

```yaml
ORCHESTRATOR_DECISION:
  solicitacao: "[resumo]"
  tipo: "[Bug Fix | Feature | Hotfix | Auditoria | Security | User Story | Refactor]"
  complexidade: "[SIMPLES | MEDIA | COMPLEXA]"
  severidade: "[Critica | Alta | Media | Baixa]"
  persona: "[IMPLEMENTER | USER_STORY_TRANSLATOR | BUGFIX_LIGHT | BUGFIX_HEAVY | AUDITOR | ADVERSARIAL | AUDITOR_SENIOR | REDTEAM | PRE_TESTER | SPEC_VALIDATOR | SPEC_IMPLEMENTER]"
  arquivos_provaveis: ["arquivo1"]
  tem_spec: "[Sim: .kiro/specs/X | Nao]"
  execucao: "[trivial | pipeline]"
  pipeline_selecionado: "[null | pipeline-[tipo]-[light|heavy]]"
  fluxo:
    - "[passo 1]"
    - "[passo 2]"
  riscos: "[riscos identificados]"
  informacao_completa: true
  lacunas: []
```

## Cadeia operacional observada

### Cadeia completa observada no ecossistema Claude

`.claude/INDEX.md` e `.claude/hooks/force-pipeline-agents.cjs` evidenciam a cadeia abaixo:

1. `task-orchestrator`
2. `information-gate`
3. `quality-gate-router`
4. `pre-tester`
5. `executor-controller`
6. `review-orchestrator`
7. `sanity-checker`
8. `final-validator`

### Cadeia contratual documentada no workspace Windsurf

Os agentes locais e os artefatos de teste evidenciam esta topologia funcional:

1. `task-orchestrator`
2. `context-classifier`
3. `orchestrator-documenter`
4. `quality-gate-router` quando ha TDD
5. `pre-tester` quando ha TDD
6. `executor-implementer`
7. `adversarial-reviewer`
8. `sanity-checker`
9. `final-validator`

### Leitura correta para o mirror Copilot

- Claude plugin runtime: possui enforcement e agentes plugin-only alem dos arquivos locais.
- Windsurf workspace: preserva contratos operacionais suficientes para baseline e handoffs.
- Copilot mirror: deve preservar a cadeia sem prometer que todos os agentes plugin-only existem como runtime nativo.

## Outputs contratuais observados

### Confirmados por agentes locais ou artefatos de teste

| Etapa | Output observado | Evidencia principal |
| --- | --- | --- |
| task-orchestrator | `ORCHESTRATOR_DECISION` | `.windsurf/CONSTITUTION.md`, `.windsurf/skills/orchestrate-task/SKILL.md` |
| context-classifier | `CONTEXT_CLASSIFICATION` | `.windsurf/agents/context-classifier.md` |
| orchestrator-documenter | `ORCHESTRATOR_PIPELINE_DECISION` | `.windsurf/agents/orchestrator-documenter.md` |
| quality-gate-router | `QUALITY_GATE_RESULT` | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` |
| pre-tester | `PRE_TESTER_RESULT` | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` |
| executor-implementer | `EXECUTOR_RESULT` | `.windsurf/agents/executor-implementer.md` |
| adversarial-reviewer | `ADVERSARIAL_RESULT` | `.windsurf/agents/adversarial-reviewer.md` |
| sanity-checker | `SANITY_RESULT` | `.windsurf/agents/sanity-checker.md` |
| final-validator | `PA_DE_CAL_*` and `FINAL_VALIDATOR_RESULT` mapping | `.windsurf/agents/final-validator.md` |

### Drift conhecido de naming

Os artefatos de teste do Claude usam `ORCHESTRATOR_DOC_RESULT` e `CLASSIFIER_RESULT`, enquanto os agentes locais Windsurf documentam `ORCHESTRATOR_PIPELINE_DECISION` e `CONTEXT_CLASSIFICATION`.

Para o mirror Copilot, isso deve ser tratado como:

- alias contratual documentado;
- nao como equivalencia perfeita de naming.

## Enforcement real versus guidance

### Enforcement real observado no Claude

- hook `UserPromptSubmit` para classificacao e reminder em `.claude/ORCHESTRATOR_ENFORCEMENT.md`;
- hook `.claude/hooks/force-pipeline-agents.cjs` para pressionar uso do pipeline;
- injecao de contexto de agentes em `.claude/hooks/agent-context-injector.cjs`;
- testes de unicidade e wiring em `.claude/hooks/__tests__/t01-ssot-uniqueness.test.cjs`.

### Guidance observada no Windsurf

- `ORCHESTRATOR_DECISION` obrigatorio em `CONSTITUTION.md`, `rules/00-core.md`, `workflows/ask.md` e `workflows/enforce.md`;
- cadeia e outputs descritos nos agentes e em artefatos de teste.

### Consequencia para o Copilot

O mirror precisa diferenciar:

- o que o Claude enforce tecnicamente;
- o que o Copilot so pode emular por instructions, prompts e agent design.

## Superficies de comando realmente evidenciadas

### Claude

`.claude/INDEX.md` evidencia:

- `/pipeline`
- `/context`
- `/commit`
- `/verify`
- `/test`

### Windsurf

`.windsurf/slash-commands/COMMANDS-INDEX.md` e workflows evidenciam:

- `/ask`
- `/context`
- `/fix`
- `/review`
- `/commit`
- `/deploy`
- `/verify`
- `/test`
- `/qa`
- `/sync-context`

Workflow real adicional evidenciado por arquivo:

- `enforce.md` -> `/enforce`

### Nao evidenciado como entry point real

- `/orchestrate`

Sem evidencia, o mirror Copilot nao deve criar esse alias.

## Invariantes minimos do mirror

### I1. Entry gate obrigatorio

Toda entrada relevante precisa emitir ou exigir `ORCHESTRATOR_DECISION` antes de analise aprofundada ou implementacao.

### I2. Ownership explicito por dimensao

- tipo, persona e severidade pertencem ao orchestrator;
- complexidade pertence ao classifier;
- selecao de pipeline pertence ao documenter.

### I3. Handoff explicito

Cada etapa precisa indicar o proximo agente ou proximo prompt.

### I4. Gates de informacao e qualidade

Lacunas de informacao e bloqueios de qualidade precisam parar ou pausar o fluxo explicitamente.

### I5. Revisao adversarial por batch

O fluxo original espera revisao adversarial proporcional antes do sanity/final validator nos cenarios nao triviais.

### I6. Contrato de saida final

O mirror nao pode declarar conclusao sem output final equivalente ao `PA_DE_CAL` ou ao `FINAL_VALIDATOR_RESULT`.

### I7. Honestidade de runtime

Nenhuma capability plugin-only do Claude pode ser declarada como enforcement nativo no Copilot.

## O que nao conta como mirror fiel

Nao conta como mirror fiel:

- copiar apenas nomes de agentes sem refletir ownership, handoffs e gates;
- esconder que partes do pipeline original dependem de plugin/hook;
- afirmar equivalencia total para `quality-gate-router` e `pre-tester` sem runtime real correspondente;
- omitir o drift conhecido do formato e naming dos outputs.

## Consequencia para os proximos batches

Os proximos batches precisam responder, capability por capability:

1. qual artefato do Copilot preserva cada invariante;
2. se essa preservacao sera `native parity`, `guided parity` ou `no parity`.