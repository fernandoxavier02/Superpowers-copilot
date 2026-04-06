---
title: Pipeline-orchestrator SSOT and mirror boundaries
area: pipeline-orchestrator-ssot
tags:
  - pipeline-orchestrator
  - ssot
  - github-copilot
  - mirror
  - governance
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir fronteiras de autoridade para o espelho `pipeline-orchestrator` no GitHub Copilot, evitando drift e duplicacao sem dono.

## Camadas oficiais

### Camada 1 - Semantic source of truth

`/.claude/` e `/.windsurf/`

Uso para este mirror:

- `/.claude/` define enforcement original, plugin wiring observado e limites do runtime Claude.
- `/.windsurf/` fornece o shape funcional observavel dos contratos, comandos e outputs.

Dentro de `/.windsurf/`, a autoridade adotada para `ORCHESTRATOR_DECISION` e:

1. `CONSTITUTION.md`
2. `skills/orchestrate-task/SKILL.md`

Arquivos derivados com drift documentado nao sao autoridade primaria para esse contrato.

### Camada 2 - Copilot adaptation source of truth

`/.githubcopilot/`

Esta e a fonte de verdade da adaptacao para GitHub Copilot.

Ela define:

- artifacts Copilot usados para cada capability;
- classificacao de paridade;
- estrategia de discovery;
- sync, validator e ativacao global.

Ela nao pode:

- reescrever a baseline original silenciosamente;
- esconder conflitos de SSOT observados na fonte;
- chamar de nativo o que e apenas guiado.

### Camada 3 - Global runtime mirror

`~/.githubcopilot-global/`

Camada derivada para runtime cross-project.

Ela nao e fonte editavel manualmente.
Toda mudanca precisa nascer em `/.githubcopilot/` e ser promovida por sync.

## Tabela de autoridade

| Tema | Autoridade primaria | Autoridade secundaria | Nao-autoridade |
| --- | --- | --- | --- |
| entrada obrigatoria via `ORCHESTRATOR_DECISION` | `/.windsurf/CONSTITUTION.md`, `/.windsurf/skills/orchestrate-task/SKILL.md` | `/.claude/ORCHESTRATOR_ENFORCEMENT.md` | `~/.githubcopilot-global/` |
| enforcement real do Claude | `/.claude/` hooks e docs | none | `/.githubcopilot/`, `~/.githubcopilot-global/` |
| chain observada do pipeline | `/.claude/INDEX.md`, `/.claude/hooks/force-pipeline-agents.cjs` | `/.windsurf/agents/README.md` | `~/.githubcopilot-global/` |
| outputs funcionais observados | `/.windsurf/agents/*.md` | `.claude/prompts/Testes/06-gates-agentes/*` | `~/.githubcopilot-global/` |
| claims de compatibilidade Copilot | `/.githubcopilot/` | none | `/.claude/`, `/.windsurf/`, `~/.githubcopilot-global/` |
| discovery do VS Code | `/.githubcopilot/` + settings locais/do usuario | none | `/.claude/`, `/.windsurf/` |
| instalacao global | `/.githubcopilot/` | `~/.githubcopilot-global/` | `/.claude/`, `/.windsurf/` |

## Resolved conflicts for this mirror

### Conflict 1 - `ORCHESTRATOR_DECISION`

**Conflito observado**

- `/.windsurf/CONSTITUTION.md` -> shape de 12 campos
- `/.windsurf/skills/orchestrate-task/SKILL.md` -> shape de 12 campos
- `/.windsurf/agents/task-orchestrator.md` -> shape divergente e reduzido

**Decisao do mirror**

- usar `CONSTITUTION.md` + `skills/orchestrate-task/SKILL.md` como baseline semantica;
- tratar `agents/task-orchestrator.md` como referencia com drift conhecido;
- nao copiar o campo `skill_detected` como se fosse parte canonica do contrato.

### Conflict 2 - output do documenter

**Conflito observado**

- testes e flow docs usam `ORCHESTRATOR_DOC_RESULT`;
- agente local documenta `ORCHESTRATOR_PIPELINE_DECISION`.

**Decisao do mirror**

- documentar ambos como alias observados;
- usar um nome adaptado unico no Copilot so quando o ledger de compatibilidade o justificar;
- nao declarar equivalencia literal sem nota de drift.

### Conflict 3 - agentes TDD intermediarios

**Conflito observado**

- `quality-gate-router` e `pre-tester` aparecem em READMEs, flows e testes;
- esses arquivos nao existem em `/.windsurf/agents/`.

**Decisao do mirror**

- tratar essas etapas como plugin-only ou contract-only, nunca como agentes locais comprovados;
- classificar a compatibilidade no Copilot como guiada ou inexistente, conforme o caso.

## Regras de promocao workspace -> global

So promover para `~/.githubcopilot-global/` quando:

- o artifact existir e estiver estavel em `/.githubcopilot/`;
- o validator do workspace passar;
- a classificacao de paridade estiver registrada;
- o approved set estiver sincronizado com o script de sync.

## Regras de drift

### Drift aceitavel

- paths absolutos de runtime global;
- variacoes de settings do usuario que nao alterem a semantica do workflow.

### Drift nao aceitavel

- mudar prompts, instructions, agents ou KB apenas no global mirror;
- alterar o approved set sem atualizar sync e validator;
- mudar claims de paridade sem atualizar a matrix.

## Politica de claims

### Claims permitidas em `/.githubcopilot/`

- como o Copilot emula gates e handoffs;
- qual artifact sustenta cada capability;
- quais gaps continuam abertos.

### Claims proibidas em `/.githubcopilot/`

- afirmar hook enforcement equivalente ao Claude;
- afirmar existencia comprovada de agentes plugin-only no runtime Copilot;
- apagar conflitos de fonte da documentacao original.

### Claims proibidas em `~/.githubcopilot-global/`

- redefinir autoridade;
- conter mudancas sem upstream em `/.githubcopilot/`;
- funcionar como origem primaria de desenvolvimento.

## Processo minimo para evitar duplicacao

1. verificar se a mudanca e semantica, adaptativa ou operacional;
2. editar apenas a camada de autoridade primaria;
3. atualizar matrix de compatibilidade se a claim mudar;
4. atualizar approved set, sync e validator se o artifact entrar no runtime;
5. so entao promover para o mirror global.