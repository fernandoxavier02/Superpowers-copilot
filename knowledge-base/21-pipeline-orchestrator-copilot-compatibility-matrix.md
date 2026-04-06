---
title: Pipeline-orchestrator Copilot compatibility matrix
area: pipeline-orchestrator-compatibility
tags:
  - pipeline-orchestrator
  - github-copilot
  - vscode
  - compatibility
  - mirroring
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Transformar a diferenca entre Claude/Windsurf e GitHub Copilot em um contrato de adaptacao explicito para o `pipeline-orchestrator`.

## Legend

- `native parity`: o Copilot suporta a capability de forma suficientemente equivalente.
- `guided parity`: o Copilot nao enforce sozinho, mas a capability pode ser reproduzida com artifacts e disciplina operacional.
- `no parity`: nao ha equivalencia pratica aceitavel sem prometer comportamento inexistente.

## Capability matrix

| Capability | Fonte canonica | Target artifact no Copilot | Paridade | Estrategia de compatibilidade | Gap residual |
| --- | --- | --- | --- | --- | --- |
| emissao inicial de `ORCHESTRATOR_DECISION` | `.windsurf/CONSTITUTION.md`, `.windsurf/skills/orchestrate-task/SKILL.md` | always-on instructions + orchestrator agent + entry prompts | guided parity | exigir bloco antes de agir e repetir contrato nos entry points | runtime nao bloqueia por evento |
| classificacao de tipo/persona/severidade | `.windsurf/skills/orchestrate-task/SKILL.md` | orchestrator agent | guided parity | centralizar classificacao no coordenador | usuario ainda pode ignorar o prompt correto |
| classificacao de complexidade por etapa separada | `.windsurf/agents/context-classifier.md` | orchestrator agent + KB + prompt handoff | guided parity | modelar classifier como etapa explicita de handoff | sem agente plugin dedicado do runtime |
| selecao de pipeline pelo documenter | `.windsurf/agents/orchestrator-documenter.md` | orchestrator agent + routing docs | guided parity | manter decisao de pipeline como ownership explicito no coordenador | naming de output fica adaptado |
| gate de informacao completa | `.windsurf/CONSTITUTION.md`, `.windsurf/agents/orchestrator-documenter.md` | instructions + prompts de entrada | guided parity | obrigar declaracao de `informacao_completa` e `lacunas` | sem pausa automatica por hook |
| gate de qualidade com aprovacao do usuario | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` | prompt de handoff + instructions | guided parity | exigir aprovacao explicita quando o fluxo declarar TDD | nao ha `quality-gate-router` nativo |
| pre-tester como etapa separada | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` | prompt/KB contract only | no parity | documentar como contrato observavel, nao como agente real do Copilot | sem runtime comprovado para agente equivalente |
| revisao adversarial por batch | `.windsurf/agents/adversarial-reviewer.md` | prompts de batch + agent team | guided parity | instruir review adversarial apos cada batch relevante | revisao continua dependente do workflow seguido |
| sanity checker proporcional | `.windsurf/agents/sanity-checker.md` | instructions + validator harness + prompts | guided parity | exigir verificacao local antes de avancar | sem gate automatico de tool runtime |
| validacao final tipo `PA_DE_CAL` | `.windsurf/agents/final-validator.md` | prompt final + orchestrator instructions | guided parity | exigir saida final estruturada antes de concluir | sem enforcement tecnico de obrigatoriedade |
| hooks de enforcement `UserPromptSubmit` | `.claude/ORCHESTRATOR_ENFORCEMENT.md` | nenhuma equivalencia direta | no parity | substituir por instructions always-on e prompts de entrada | nao ha hook equivalente comprovado no Copilot para este caso |
| injecao contextual por subagente | `.claude/hooks/agent-context-injector.cjs` | KB + instructions + agent order of reading | no parity | trocar injecao por ordem de leitura explicita | perde granularidade de evento |
| agent team coordinator + workers | runtime Copilot + mirror superpowers existente | `.agent.md` + prompts | guided parity | usar coordenador e workers do Copilot quando discovery estiver configurado | disponibilidade depende de settings e UX |
| workspace source -> global mirror | mirror superpowers existente | sync script + validator + user settings check | guided parity | reutilizar padrao aprovado do superpowers | ativacao global continua dependente de settings do usuario |
| discovery via `.vscode/settings.json` | workspace Copilot existente | settings locais e validacao de settings do usuario | native parity | usar superficies reais de discovery do VS Code | nenhum, desde que settings estejam configurados |

## Command-surface matrix

| Surface | Evidencia | Acao no mirror Copilot | Paridade |
| --- | --- | --- | --- |
| `/ask` | `.windsurf/slash-commands/COMMANDS-INDEX.md`, `.windsurf/workflows/ask.md` | criar prompt equivalente | guided parity |
| `/enforce` | `.windsurf/workflows/enforce.md` | criar prompt equivalente | guided parity |
| `/pipeline` | `.claude/INDEX.md`, `.claude/hooks/force-pipeline-agents.cjs` | criar prompt equivalente com nota de limitacao | guided parity |
| `/orchestrate` | nao evidenciado | nao criar alias | no parity |

## Claims proibidas

O mirror Copilot nao pode alegar:

- enforcement automatico de `ORCHESTRATOR_DECISION` por hook de ciclo de vida;
- existencia local comprovada de `quality-gate-router` e `pre-tester` como agentes runtime do Copilot;
- equivalencia exata de naming entre todos os outputs do Claude/Windsurf e os outputs adaptados;
- bloqueio automatico de conclusao sem validacao final por parte do runtime.

## Claims permitidas

O mirror Copilot pode alegar:

- cadeia operacional preservada por prompts, instructions, KB e agente coordenador;
- gates centrais emulados como compatibilidade guiada;
- sync workspace -> global e harness de validacao dedicados;
- discovery real quando settings locais e do usuario apontarem para os diretivos corretos.

## Consequencia pratica

Todo artifact operacional dos batches seguintes precisa apontar para uma linha desta matriz.

Se uma capability nao tiver linha aqui, o mirror ainda nao tem autorizacao para implementa-la ou anuncia-la.