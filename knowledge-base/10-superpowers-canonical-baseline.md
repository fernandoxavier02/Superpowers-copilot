---
title: Superpowers canonical baseline
area: superpowers-baseline
tags:
  - superpowers
  - claude
  - windsurf
  - copilot
  - mirroring
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Congelar a baseline canônica do sistema `superpowers` antes de qualquer adaptação para GitHub Copilot.

Este arquivo não descreve a implementação Copilot. Ele descreve o que precisa ser preservado do sistema original para que o espelho seja considerado fiel em comportamento.

## Escopo da baseline

Esta baseline cobre:

- cadeia de ativação;
- handoffs obrigatórios;
- hard gates;
- stop conditions;
- verificação final obrigatória;
- artefatos-fonte que hoje representam o comportamento canônico.

Ela não cobre ainda:

- qual artefato do Copilot implementará cada parte;
- estratégia de compatibilidade para gaps de runtime;
- rollout workspace/global.

## Fontes canônicas mínimas

### Cadeia `superpowers`

- `.windsurf/skills/superpower-brainstorming/SKILL.md`
- `.windsurf/skills/superpower-writing-plans/SKILL.md`
- `.windsurf/skills/superpower-subagent-driven-development/SKILL.md`
- `.windsurf/skills/superpower-executing-plans/SKILL.md`
- `.windsurf/skills/superpower-verification-before-completion/SKILL.md`
- `.windsurf/workflows/super.md`

### Enforcement e wiring do sistema original

- `.claude/settings.json`
- `.claude/ORCHESTRATOR_ENFORCEMENT.md`
- `.claude/agents/task-orchestrator.md`

### Validador existente de fidelidade

- `scripts/validate_superpowers_fidelity.py`

## Cadeia canônica mínima

O fluxo mínimo fiel é:

1. `superpower-brainstorming`
2. `superpower-writing-plans`
3. execução em um dos dois caminhos:
   - `superpower-subagent-driven-development`
   - `superpower-executing-plans`
4. `superpower-verification-before-completion`

### Regras obrigatórias da cadeia

- não pular de brainstorming direto para execução;
- não começar implementação sem plano aprovado;
- não declarar sucesso sem verificação final fresca;
- não substituir handoff formal por improvisação silenciosa.

## Handoffs obrigatórios

| Etapa de origem | Etapa de destino | Regra canônica |
| --- | --- | --- |
| brainstorming | writing-plans | brainstorming termina apenas quando o próximo passo é escrever o plano |
| writing-plans | subagent-driven-development | caminho preferencial quando a plataforma suporta subagentes úteis |
| writing-plans | executing-plans | fallback quando execução em batches for mais adequada |
| subagent-driven-development | verification-before-completion | obrigatório antes de concluir |
| executing-plans | verification-before-completion | obrigatório antes de concluir |

## Hard gates canônicos

### Gate 1: design antes do código

- brainstorming ou equivalente precisa fechar lacunas relevantes de design antes de qualquer implementação.

### Gate 2: plano aprovado

- `writing-plans` exige plano explícito, com handoff formal, antes de implementação.

### Gate 3: execução com checkpoints

- execução precisa ter stop conditions e não pode atravessar bloqueios sem escalonar para o usuário.

### Gate 4: verificação final obrigatória

- qualquer claim de conclusão depende de `verification-before-completion` ou equivalente comportamental.

## Stop conditions canônicas

Parar imediatamente quando:

- houver bloqueio material;
- o plano tiver lacuna crítica;
- a instrução estiver ambígua;
- a verificação falhar repetidamente;
- a abordagem precisar ser refeita.

## Invariantes de fidelidade

Um espelho Copilot só pode ser considerado fiel se preservar os invariantes abaixo.

### I1. Activation order preservada

O usuário não deve ser incentivado a executar implementação antes de brainstorming/planejamento quando o problema exigir isso.

### I2. Handoff explícito

As transições entre etapas precisam ser visíveis e nomeadas. Não basta que o comportamento “aconteça por acaso”.

### I3. Gate de plano aprovado

O fluxo precisa deixar explícito que plano aprovado é pré-requisito para implementação.

### I4. Stop condition real

O espelho precisa parar diante de bloqueios relevantes. Se o runtime não bloqueia sozinho, isso precisa virar compatibilidade guiada explícita.

### I5. Verificação final obrigatória

Nenhuma conclusão legítima pode prescindir da etapa de verificação final.

### I6. Honestidade de runtime

Se o Copilot não oferece enforcement nativo equivalente ao Claude, o espelho não pode afirmar paridade total nesse ponto.

## O que conta como espelho fiel

Conta como espelho fiel:

- preservar a cadeia de ativação;
- preservar os gates e as stop conditions em comportamento ou compatibilidade claramente declarada;
- preservar discoverability dos principais entry points;
- preservar a distinção entre planejar, executar e verificar.

Não conta como espelho fiel:

- copiar nomes de arquivos sem reproduzir a cadeia real;
- declarar gates que o runtime não enforce e não marcar isso como limitação;
- empilhar prompts, skills e agents redundantes que confundam o usuário;
- transformar `superpowers` em apenas um catálogo estático sem handoff operacional.

## Consequência prática para a camada Copilot

Todo próximo artefato do espelho Copilot deve responder a duas perguntas:

1. qual invariante canônico ele preserva;
2. se a preservação é nativa, guiada ou impossível no runtime do Copilot.

Essa distinção é obrigatória para evitar “fidelidade falsa”.