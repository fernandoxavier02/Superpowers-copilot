---
title: Superpowers global profile activation
area: superpowers-global-activation
tags:
  - superpowers
  - global-mirror
  - settings
  - activation
  - operations
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir como ativar o mirror `superpowers` no perfil do usuário sem criar ambiguidade entre discovery local do workspace e discovery global cross-project.

## Pré-condições

1. o workspace source em `/.githubcopilot/` passou no harness;
2. o mirror em `~/.githubcopilot-global/` foi sincronizado pelo script versionado;
3. o usuário aceita tratar o mirror global como derivado, não editável manualmente.

## Snippet recomendado

Use paths absolutos no settings do usuário para evitar depender de expansão de variáveis específica do runtime.

O preflight do harness para `--user-settings` assume explicitamente esse modelo: ele valida o arquivo de settings como configuração do workflow `superpowers` completo, não apenas uma descoberta parcial de artifacts.

Para esse fluxo completo, os três arrays abaixo são obrigatórios:

- `chat.promptFilesLocations`
- `chat.agentFilesLocations`
- `chat.instructionsFilesLocations`

O Copilot pode operar com discovery parcial em cenários genéricos, mas este preflight trata ausência de qualquer um deles como falha porque o workflow `superpowers` completo depende das três superfícies juntas.

### Windows example

```json
{
  "chat.promptFilesLocations": [
    "C:/Users/<user>/.githubcopilot-global/prompts"
  ],
  "chat.agentFilesLocations": [
    "C:/Users/<user>/.githubcopilot-global/agents"
  ],
  "chat.instructionsFilesLocations": [
    "C:/Users/<user>/.githubcopilot-global/instructions"
  ]
}
```

### POSIX example

```json
{
  "chat.promptFilesLocations": [
    "/home/<user>/.githubcopilot-global/prompts"
  ],
  "chat.agentFilesLocations": [
    "/home/<user>/.githubcopilot-global/agents"
  ],
  "chat.instructionsFilesLocations": [
    "/home/<user>/.githubcopilot-global/instructions"
  ]
}
```

## Prioridade de discovery

No workflow suportado aqui, o settings do usuário precisa apenas apontar para o mirror global.

Quando um repositório também materializa `.vscode/settings.json` com paths locais, a precedência local continua sendo controlada no próprio workspace, sem exigir paths relativos adicionais no settings do usuário.

## Escopo do preflight

O preflight de settings não tenta provar invocabilidade real no picker do VS Code. Ele só valida três condições operacionais mínimas:

1. o arquivo de settings é parseável;
2. os arrays de prompts, agents e instructions existem;
3. cada array contém o path exato esperado para o `--global-root` usado na validação.

## Promotion flow

1. validar `/.githubcopilot/` no workspace;
2. sincronizar para `~/.githubcopilot-global/` com `scripts/sync-githubcopilot-superpowers-to-global.ps1`;
3. validar o mirror global com `--require-global`;
4. validar o settings do usuário com `--user-settings`;
5. só então considerar o runtime global pronto.

## Preflight command

```powershell
python scripts/validate_githubcopilot_superpowers_mirror.py --require-global --user-settings "$env:APPDATA/Code/User/settings.json"
```

## Failure modes

- mirror global existe, mas o settings do usuário não aponta para ele;
- paths do settings usam diretórios divergentes do destino sincronizado;
- o perfil global é editado manualmente e passa a divergir do workspace source;
- o settings do workspace mascara problemas do perfil global durante testes locais.

## Rollback

1. reverter a mudança no workspace source;
2. sincronizar novamente para o mirror global;
3. rerodar o harness com `--require-global` e `--user-settings`.