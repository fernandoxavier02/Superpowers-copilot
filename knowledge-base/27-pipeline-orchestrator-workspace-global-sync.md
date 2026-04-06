---
title: Pipeline-orchestrator workspace global sync
area: pipeline-orchestrator-sync
tags:
  - pipeline-orchestrator
  - sync
  - global-mirror
  - github-copilot
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir como o mirror `pipeline-orchestrator` e promovido do workspace versionado em `/.githubcopilot/` para o runtime derivado em `~/.githubcopilot-global/`.

## Promotion policy

O mirror global e sempre derivado do workspace.

Regras:

- editar somente `/.githubcopilot/`;
- validar o workspace antes do sync;
- copiar apenas o approved file set;
- remover arquivos stale que tenham sido gerenciados por versoes anteriores deste mesmo mirror;
- falhar de forma segura se o manifest ou o state estiverem corrompidos;
- nao tratar `~/.githubcopilot-global/` como fonte editavel manualmente.

O approved file set operacional deve ser mantido em um manifest unico para tooling:

- `/.githubcopilot/mirror-manifests/pipeline-orchestrator-approved-files.json`

Esse manifest existe para reduzir drift entre documentacao, script de sync e validator. A lista textual abaixo continua sendo a representacao humana auditavel do mesmo conjunto.

## Approved file set

### Prompts

- `prompts/README.md`
- `prompts/ask.prompt.md`
- `prompts/enforce.prompt.md`
- `prompts/pipeline.prompt.md`
- `prompts/skah.prompt.md`

### Agents

- `agents/README.md`
- `agents/pipeline-orchestrator.agent.md`

### Instructions

- `instructions/README.md`
- `instructions/50-pipeline-orchestrator-gates.instructions.md`

### Knowledge base

- `knowledge-base/00-index.md`
- `knowledge-base/20-pipeline-orchestrator-canonical-baseline.md`
- `knowledge-base/21-pipeline-orchestrator-copilot-compatibility-matrix.md`
- `knowledge-base/22-pipeline-orchestrator-mirror-batches.md`
- `knowledge-base/23-pipeline-orchestrator-ssot-and-mirror-boundaries.md`
- `knowledge-base/24-pipeline-orchestrator-packaging-and-discovery.md`
- `knowledge-base/25-pipeline-orchestrator-routing-and-handoffs.md`
- `knowledge-base/26-pipeline-orchestrator-gate-emulation.md`
- `knowledge-base/27-pipeline-orchestrator-workspace-global-sync.md`
- `knowledge-base/28-pipeline-orchestrator-validation-harness.md`
- `knowledge-base/29-pipeline-orchestrator-global-profile-activation.md`

## Sync command

```powershell
pwsh -File scripts/sync-githubcopilot-pipeline-orchestrator-to-global.ps1
```

### Com root global explicito

```powershell
pwsh -File scripts/sync-githubcopilot-pipeline-orchestrator-to-global.ps1 -GlobalRoot "$HOME/.githubcopilot-global"
```

### Sem preflight

```powershell
pwsh -File scripts/sync-githubcopilot-pipeline-orchestrator-to-global.ps1 -SkipValidation
```

## Prune policy

O sync mantem um estado proprio do mirror em:

- `~/.githubcopilot-global/.mirror-state/pipeline-orchestrator-managed-files.json`

Esse estado existe para permitir prune seguro:

- remover apenas arquivos stale previamente gerenciados pelo mirror `pipeline-orchestrator`;
- nao tocar em artifacts de outros mirrors, como `superpowers`.

Se o state estiver corrompido, o sync deve falhar em vez de inferir stale files cegamente.

## Rollback

### Rollback de workspace

Reverter em `/.githubcopilot/` e sincronizar novamente.

### Rollback de global mirror

Executar novamente o sync com a ultima versao aprovada do workspace.

## Consequencia para o validator

O validator deve considerar incompleto qualquer global mirror que nao contenha integralmente o approved file set acima.