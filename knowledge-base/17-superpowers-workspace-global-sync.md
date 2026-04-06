---
title: Superpowers workspace global sync
area: superpowers-sync
tags:
  - superpowers
  - sync
  - global-mirror
  - drift
  - operations
globs:
  - "**"
updated: 2026-04-06
---

## Objetivo

Definir como o espelho `superpowers` sai de `/.githubcopilot/` e é promovido para `~/.githubcopilot-global/` sem virar uma segunda fonte de verdade.

## Source and target

### Source of truth

`/.githubcopilot/`

### Runtime mirror target

`~/.githubcopilot-global/`

## O que deve ser promovido

O mirror global deve conter apenas o conjunto necessário para descoberta e execução cross-project do workflow `superpowers`.

Como regra operacional, a lista abaixo é o conjunto aprovado e autoritativo para o script de sync e para o harness em modo `--require-global`.

Approved set version: `2026-04-06-native-skills`

### Prompts

- `prompts/README.md`
- `prompts/super.prompt.md`
- `prompts/super-brainstorm.prompt.md`
- `prompts/super-plan.prompt.md`
- `prompts/super-subagent.prompt.md`
- `prompts/super-execute.prompt.md`
- `prompts/super-verify.prompt.md`
- `prompts/super-tdd.prompt.md`
- `prompts/super-debug.prompt.md`
- `prompts/super-review.prompt.md`
- `prompts/super-finish.prompt.md`
- `prompts/super-parallel.prompt.md`
- `prompts/superpowers-start.prompt.md`
- `prompts/superpowers-brainstorming.prompt.md`
- `prompts/superpowers-writing-plans.prompt.md`
- `prompts/superpowers-subagent-driven-development.prompt.md`
- `prompts/superpowers-executing-plans.prompt.md`
- `prompts/superpowers-verification-before-completion.prompt.md`
- `prompts/superpowers-git-worktrees.prompt.md`
- `prompts/superpowers-finishing-branch.prompt.md`
- `prompts/superpowers-parallel-agents.prompt.md`
- `prompts/superpowers-receiving-review.prompt.md`

### Agents

- `agents/README.md`
- `agents/superpowers-orchestrator.agent.md`

### Instructions

- `instructions/README.md`
- `instructions/35-superpowers-bootstrap.instructions.md`
- `instructions/40-superpowers-gates.instructions.md`

### Knowledge base

- `knowledge-base/00-index.md`
- `knowledge-base/10-superpowers-canonical-baseline.md`
- `knowledge-base/11-superpowers-copilot-compatibility-matrix.md`
- `knowledge-base/12-superpowers-mirror-batches.md`
- `knowledge-base/13-superpowers-ssot-and-mirror-boundaries.md`
- `knowledge-base/14-superpowers-packaging-and-discovery.md`
- `knowledge-base/15-superpowers-routing-and-handoffs.md`
- `knowledge-base/16-superpowers-gate-emulation.md`
- `knowledge-base/17-superpowers-workspace-global-sync.md`
- `knowledge-base/18-superpowers-validation-harness.md`
- `knowledge-base/19-superpowers-global-profile-activation.md`
- `knowledge-base/20-superpowers-plugin-bridge.md`

### Skills (native layer — verbatim from obra/superpowers HEAD)

- `skills/brainstorming/SKILL.md`
- `skills/dispatching-parallel-agents/SKILL.md`
- `skills/executing-plans/SKILL.md`
- `skills/finishing-a-development-branch/SKILL.md`
- `skills/receiving-code-review/SKILL.md`
- `skills/requesting-code-review/SKILL.md`
- `skills/subagent-driven-development/SKILL.md`
- `skills/systematic-debugging/SKILL.md`
- `skills/test-driven-development/SKILL.md`
- `skills/using-git-worktrees/SKILL.md`
- `skills/using-superpowers/SKILL.md`
- `skills/using-superpowers/references/copilot-tools.md`
- `skills/using-superpowers/references/copilot-tools-windows.md`
- `skills/verification-before-completion/SKILL.md`
- `skills/writing-plans/SKILL.md`
- `skills/writing-skills/SKILL.md`
- `skills/brainstorming/visual-companion.md`
- `skills/brainstorming/spec-document-reviewer-prompt.md`
- `skills/requesting-code-review/code-reviewer.md`

## O que não deve ser promovido automaticamente

- artifacts experimentais ainda não validados;
- docs não necessários para descoberta e execução do workflow;
- qualquer ajuste manual feito só no perfil global.

## Promotion policy

Promova para o mirror global apenas quando:

1. os artifacts do workspace estiverem sem erros de editor;
2. o validador estrutural do workspace estiver passando;
3. o change set estiver coerente com a baseline e a matriz de compatibilidade;
4. o destino global for tratado como derivado, não editado manualmente.

## Drift policy

### Drift permitido

- settings locais do usuário que só habilitam discovery;
- arquivos auxiliares fora do conjunto aprovado listado acima, fora do alcance do script de sync e fora dos diretórios gerenciados `prompts/`, `agents/`, `instructions/` e `knowledge-base/`, como logs, caches ou diagnostics locais.

Todos os arquivos README promovidos pelo sync fazem parte do conjunto gerenciado, não da categoria auxiliar.

### Drift proibido

- alterar prompts, instructions, agents ou knowledge-base apenas em `~/.githubcopilot-global/`;
- alterar qualquer arquivo README promovido pelo sync apenas em `~/.githubcopilot-global/`;
- criar novas claims de enforcement ou compatibilidade no mirror global;
- deixar o mirror com coverage menor do que o conjunto aprovado no workspace.

## Layout esperado do mirror global

```text
~/.githubcopilot-global/
  prompts/
  agents/
  instructions/
  knowledge-base/
  skills/
```

## Settings mínimos para discovery

Para uso cross-project do mirror promovido, o usuário deve garantir no VS Code que os settings relevantes apontem para `~/.githubcopilot-global/`:

- `chat.promptFilesLocations`
- `chat.agentFilesLocations`
- `chat.instructionsFilesLocations`
- `chat.agentSkillsLocations` quando skills forem usadas

No workspace deste repositório, a baseline de discovery local já foi materializada em `.vscode/settings.json`. Esses paths locais continuam sendo responsabilidade do próprio workspace, não um requisito adicional do settings global do usuário.

## Operational flow

1. editar e validar em `/.githubcopilot/`;
2. executar o script de sync com preflight de validação habilitado;
3. validar o mirror global com o harness em modo global obrigatório;
4. quando houver settings de perfil explícitos, validar também o arquivo do usuário com o harness;
5. só então tratar o espelho como pronto para uso cross-project.

## Activation handoff

Depois da promoção estrutural, a ativação real do runtime depende do settings de perfil do usuário apontando para `~/.githubcopilot-global/`.

O snippet recomendado e o checklist de ativação ficam em `knowledge-base/19-superpowers-global-profile-activation.md`.

## Rollback

Para rollback:

1. revert no workspace source;
2. reexecute o sync;
3. rode o validador novamente no global mirror.