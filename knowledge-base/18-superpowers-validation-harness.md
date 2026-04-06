---
title: Superpowers validation harness
area: superpowers-validation
tags:
  - superpowers
  - validation
  - harness
  - drift
  - fidelity
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir o conjunto mínimo de validações para distinguir existência estrutural de fidelidade operacional do espelho `superpowers` no Copilot.

## O que o harness cobre

### Workspace structure

Verifica se os artifacts essenciais existem em `/.githubcopilot/`.

Para arquivos do approved set que não estão em `WORKSPACE_CHECKS`, o harness valida existência e BOM, não snippets específicos.

### Global mirror structure

Verifica se o mirror em `~/.githubcopilot-global/` contém o conjunto aprovado de arquivos.

### Snippet fidelity heurística

Verifica se prompts, instructions, agents e knowledge-base mantêm snippets mínimos de gates, handoffs e claims do workflow.

Isso não substitui revisão humana nem prova equivalência semântica completa do runtime.

O harness também não distingue intenção semântica de contexto textual: ele valida presença literal dos snippets exigidos, não se eles aparecem em comentário, exemplo ou bloco operativo.

### Encoding sanity

Verifica se os arquivos markdown validados não estão com BOM UTF-8.

## O que o harness não cobre ainda

- invocabilidade real no slash command picker do VS Code;
- comportamento do runtime sob todas as combinações de settings do usuário;
- performance, UX e fricção de uso em sessões longas.

## Commands

### Workspace only

```powershell
python scripts/validate_githubcopilot_superpowers_mirror.py
```

### Workspace + global mirror obrigatório

```powershell
python scripts/validate_githubcopilot_superpowers_mirror.py --require-global
```

### Com caminho global explícito

```powershell
python scripts/validate_githubcopilot_superpowers_mirror.py --global-root "$HOME/.githubcopilot-global" --require-global
```

### Com settings de perfil explícitos

```powershell
python scripts/validate_githubcopilot_superpowers_mirror.py --require-global --user-settings "$env:APPDATA/Code/User/settings.json"
```

Esse modo é um preflight para o workflow `superpowers` completo. Ele exige que prompts, agents e instructions do mirror global estejam explicitamente referenciados no settings do usuário.

Ele não exige paths relativos de workspace no settings do usuário. Overrides locais continuam pertencendo ao `.vscode/settings.json` do próprio repositório quando existirem.

## Success criteria

O harness só deve retornar `OK` quando:

1. os arquivos obrigatórios existem;
2. snippets críticos de cadeia, gate e handoff existem;
3. não há BOM nos arquivos validados;
4. o mirror global, quando exigido, não está incompleto;
5. o settings do usuário, quando fornecido, contém referências explícitas para os diretórios `prompts`, `agents` e `instructions` do `--global-root` informado.

## Uso recomendado

1. após editar a camada `/.githubcopilot/`;
2. após sincronizar para o global mirror;
3. após configurar o settings de perfil do usuário;
4. antes de declarar o espelho pronto para uso cross-project.