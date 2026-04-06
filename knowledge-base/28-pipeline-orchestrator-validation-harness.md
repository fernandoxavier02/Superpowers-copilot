---
title: Pipeline-orchestrator validation harness
area: pipeline-orchestrator-validation
tags:
  - pipeline-orchestrator
  - validation
  - harness
  - drift
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir o conjunto minimo de validacoes para distinguir existencia estrutural de fidelidade operacional do mirror `pipeline-orchestrator` no Copilot.

## O que o harness cobre

### Workspace structure

Verifica se os artifacts essenciais do mirror existem em `/.githubcopilot/`.

Tambem verifica se o manifest do approved set bate com a lista textual documentada no KB de sync.

### Global mirror structure

Verifica se `~/.githubcopilot-global/` contem o approved file set do mirror `pipeline-orchestrator` quando o modo global for exigido.

Tambem verifica se o state file do mirror global continua alinhado ao manifest aprovado:

- `~/.githubcopilot-global/.mirror-state/pipeline-orchestrator-managed-files.json`

### Snippet fidelity heuristica

Verifica se prompts, instructions, agents e knowledge-base mantem snippets minimos sobre:

- `ORCHESTRATOR_DECISION`;
- routing e handoffs;
- gates e limites de paridade;
- approved set e policy de promotion.

Isso nao prova equivalencia semantica completa de runtime. O harness valida presenca estrutural e consistencia minima do espelho.

### Approved-set consistency

Verifica se:

- o manifest do approved set existe;
- o script de sync usa esse manifest como fonte de verdade operacional;
- a lista humana documentada no KB de sync permanece alinhada ao mesmo conjunto.

### Sync hardening heuristica

Verifica se o script de sync preserva sinais minimos do hardening operacional, incluindo:

- state file em `.mirror-state/`;
- prune explicito de stale files;
- aviso explicito quando `-SkipValidation` for usado.

### Encoding sanity

Verifica ausencia de BOM UTF-8 nos markdowns validados.

### User settings audit

Quando solicitado, verifica se o settings do usuario aponta explicitamente para:

- `~/.githubcopilot-global/prompts`
- `~/.githubcopilot-global/agents`
- `~/.githubcopilot-global/instructions`

## Commands

### Workspace only

```powershell
python scripts/validate_githubcopilot_pipeline_orchestrator_mirror.py --workspace-only
```

### Workspace + global obrigatorio

```powershell
python scripts/validate_githubcopilot_pipeline_orchestrator_mirror.py --require-global
```

### Global root explicito

```powershell
python scripts/validate_githubcopilot_pipeline_orchestrator_mirror.py --global-root "$HOME/.githubcopilot-global" --require-global
```

### Auditando user settings

```powershell
python scripts/validate_githubcopilot_pipeline_orchestrator_mirror.py --require-global --user-settings "$env:APPDATA/Code/User/settings.json"
```

## Success criteria

O harness so retorna `OK` quando:

1. os arquivos obrigatorios existem;
2. snippets criticos do workflow existem;
3. nao ha BOM nos arquivos validados;
4. o mirror global, quando exigido, nao esta incompleto;
5. o settings do usuario, quando fornecido, referencia prompts, agents e instructions do global root informado.
6. o state file global do mirror nao esta em drift com o manifest aprovado.

## Uso recomendado

1. apos editar `/.githubcopilot/`;
2. antes do sync global;
3. apos o sync global;
4. antes de declarar o mirror pronto para uso cross-project.