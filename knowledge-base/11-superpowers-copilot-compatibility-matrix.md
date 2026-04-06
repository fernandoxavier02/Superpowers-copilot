---
title: Superpowers Copilot compatibility matrix
area: superpowers-compatibility
tags:
  - superpowers
  - github-copilot
  - vscode
  - compatibility
  - mirroring
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Transformar a diferença entre o runtime Claude/Windsurf e o runtime GitHub Copilot em um contrato explícito de adaptação.

Este arquivo existe para impedir duas falhas:

- planejar paridade impossível como se fosse nativa;
- degradar demais o espelho por não distinguir capability de artefato.

## Legend

- **native parity**: o Copilot suporta a capability de forma suficientemente equivalente.
- **guided parity**: o Copilot não enforce nativamente, mas a capability pode ser reproduzida por combinação de artifacts e disciplina operacional.
- **no parity**: não há equivalência prática aceitável sem alterar o comportamento esperado.

## Capability matrix

| Capability | Fonte canônica | Target artifact no Copilot | Paridade | Estratégia de compatibilidade | Gap residual |
| --- | --- | --- | --- | --- | --- |
| activation order | `.windsurf/workflows/super.md` | prompt files + skills + orchestrator agent | guided parity | explicitar a ordem nos entry points e no coordenador | usuário ainda pode ignorar a ordem se invocar artefato errado |
| brainstorming handoff | `superpower-brainstorming` | skill/prompt de entrada | guided parity | forçar saída com próximo passo explícito para planning | sem hook de bloqueio automático |
| writing-plans handoff | `superpower-writing-plans` | skill de planning | guided parity | plano termina com handoff nomeado para execution path | depende de observância do fluxo pelo agente |
| plan approval gate | `superpower-writing-plans` | instructions always-on + prompt de execução | guided parity | exigir aprovação explícita antes de execução | runtime não impede 100% por evento |
| execution routing | `superpower-subagent-driven-development` / `superpower-executing-plans` | orchestrator agent + execution skills | guided parity | coordenador escolhe caminho conforme capacidade de subagentes e batching | sem plugin orquestrador equivalente ao Claude |
| verification-before-completion | `superpower-verification-before-completion` | skill/prompt final + instructions de quality | guided parity | tornar obrigatório o handoff para verificação em todo caminho de execução | claim final ainda depende da honestidade do agente |
| lifecycle hooks | `.claude/settings.json` | hooks nativos do VS Code + instructions + prompts | no parity | emular apenas o subconjunto viável; documentar limites | sem equivalência 1:1 de enforcement e injeção contextual |
| orchestrator decision block | `.claude/ORCHESTRATOR_ENFORCEMENT.md` | instructions + orchestrator agent | guided parity | exigir `ORCHESTRATOR_DECISION` como contrato de workflow | sem bloqueio garantido por evento do runtime |
| context injection before tools | `.claude/hooks/*` | instructions + KB + agent design | no parity | substituir por KB e regras de leitura antecipada | perde granularidade por evento |
| subagent execution | cadeia Claude/Copilot agents | `.agent.md` coordinator + workers | native parity parcial | usar padrão coordinator + workers do Copilot | disponibilidade real depende de discovery e settings |
| panel-of-experts | panel agents Claude | múltiplos agents + review prompts | guided parity | reproduzir por equipe de agentes e revisão adversarial em batch | consolidação automática é mais fraca |
| progress tracking | pipeline docs Claude | orchestrator output contract | guided parity | exigir status visível por batch | sem suporte de runtime dedicado |
| workspace/global mirror | padrão multi-env do repo | `.githubcopilot/` + `~/.githubcopilot-global/` | guided parity | usar source-of-truth local, script de sync e validação explícita do mirror | discovery global ainda depende de configuração do usuário/perfil |

## Artifact mapping heurístico

### Use `*.instructions.md` quando

- a regra for estável;
- a regra precisar ficar always-on;
- a capability for transversal, mas curta;
- a adaptação for guardrail e não workflow completo.

### Use `.prompt.md` quando

- a etapa for manual, invocável por slash command;
- houver necessidade de entry point claro;
- o objetivo for guiar o usuário a entrar na cadeia certa.

### Use `SKILL.md` quando

- a capability for reutilizável e portátil;
- a etapa representar um workflow nomeado da cadeia `superpowers`;
- houver supporting files e referências auxiliares.

### Use `.agent.md` quando

- houver papel recorrente e especializado;
- a etapa precisar de coordenação ou decisão entre caminhos;
- a compatibilidade exigir coordinator + workers.

## Residual gaps que não podem ser escondidos

### G1. Hooks de enforcement

Claude usa hooks de ciclo de vida para empurrar e bloquear comportamento. O Copilot não oferece paridade direta suficiente para afirmar equivalência total.

### G2. Gate realmente obrigatório

No Copilot, parte da disciplina será por convenção operacional e design de artifacts. Isso é compatibilidade guiada, não enforcement total.

### G3. Discovery real dos agentes locais

Os agentes em `.githubcopilot/agents/` existem como artefatos válidos, mas a invocabilidade real depende de settings de discovery no VS Code.

### G4. Consolidação de painéis complexos

É possível aproximar o comportamento do panel de especialistas, mas a ergonomia e o enforcement não são equivalentes ao ecossistema Claude com plugins e hooks próprios.

## Regras de uso desta matriz

1. Nenhum batch pode chamar uma capability de “paridade total” sem `native parity` ou evidência operacional equivalente.
2. Toda linha marcada como `guided parity` deve apontar para o artifact de compatibilidade correspondente.
3. Toda linha marcada como `no parity` precisa virar limitação conhecida ou redesign explícito.

## Próximo uso esperado

O próximo batch deve transformar esta matriz em:

- boundaries de SSOT e mirror;
- mapa de artifacts Copilot;
- batches de implementação com revisão adversarial por capability.