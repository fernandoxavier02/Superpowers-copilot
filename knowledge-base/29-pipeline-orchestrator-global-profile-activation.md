---
title: Pipeline-orchestrator global profile activation
area: pipeline-orchestrator-global-activation
tags:
  - pipeline-orchestrator
  - global-profile
  - settings
  - github-copilot
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Documentar como ativar o mirror `pipeline-orchestrator` no perfil global do usuario para uso cross-project no GitHub Copilot.

## Pre-condicoes

Antes de ativar no perfil global:

1. o validator do workspace deve passar;
2. o sync para `~/.githubcopilot-global/` deve ter sido executado;
3. o validator com `--require-global` deve passar.

## Snippet recomendado

Adicionar no settings do usuario do VS Code os diretorios do global mirror:

```json
{
  "chat.promptFilesLocations": [
    "${userHome}/.githubcopilot-global/prompts"
  ],
  "chat.agentFilesLocations": [
    "${userHome}/.githubcopilot-global/agents"
  ],
  "chat.instructionsFilesLocations": [
    "${userHome}/.githubcopilot-global/instructions"
  ]
}
```

## Observacoes

- esse snippet ativa prompts, agents e instructions do mirror global;
- ele nao substitui o `.vscode/settings.json` do workspace atual quando o usuario quiser manter overrides locais;
- o validator do mirror aceita arrays com mais de um path, desde que os diretorios do global mirror estejam presentes.

## Sequencia recomendada

1. validar o workspace;
2. sincronizar para `~/.githubcopilot-global/`;
3. validar o global mirror;
4. validar o settings do usuario;
5. so entao declarar o mirror pronto para uso cross-project.