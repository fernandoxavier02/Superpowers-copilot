---
title: Pipeline-orchestrator gate emulation
area: pipeline-orchestrator-gates
tags:
  - pipeline-orchestrator
  - gates
  - enforcement
  - parity
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Mapear quais gates do `pipeline-orchestrator` original sao nativos, guiados ou impossiveis no runtime do GitHub Copilot.

## Claims versus enforcement

| Gate | Fonte original | Enforcement original | Emulacao no Copilot | Paridade |
| --- | --- | --- | --- | --- |
| `ORCHESTRATOR_DECISION` antes da acao | `.claude/ORCHESTRATOR_ENFORCEMENT.md`, `.windsurf/CONSTITUTION.md` | hook + meta-prompt + workflow | instructions + prompts + agent | guided parity |
| classificacao disciplinada | `.windsurf/skills/orchestrate-task/SKILL.md` | workflow/agent | agent coordenador + prompt contract | guided parity |
| info gate com pausa por lacuna | `.windsurf/agents/orchestrator-documenter.md` | etapa dedicada do workflow | `informacao_completa` + `lacunas` + stop textual | guided parity |
| quality gate com aprovacao explicita | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` | etapa plugin/workflow | prompt contract + user approval explicit | guided parity |
| pre-tester separado | `.claude/prompts/Testes/06-gates-agentes/pipeline-flow.md` | etapa plugin/workflow | apenas documentacao de contrato | no parity |
| revisao adversarial por batch | `.windsurf/agents/adversarial-reviewer.md` | agente dedicado | batch checklist + coordinator discipline | guided parity |
| sanity checker proporcional | `.windsurf/agents/sanity-checker.md` | agente dedicado + comandos reais | validation prompts + harness + checks locais | guided parity |
| final validator com saida estruturada | `.windsurf/agents/final-validator.md` | agente dedicado | prompt/agent output contract | guided parity |
| bloqueio por hook no submit | `.claude/ORCHESTRATOR_ENFORCEMENT.md` | hook de evento | sem equivalente comprovado | no parity |
| injecao de contexto por tool event | `.claude/hooks/agent-context-injector.cjs` | hook de tool event | ordem de leitura e KB | no parity |

## Sinais de violacao

O espelho deve considerar que houve violacao quando:

- a resposta relevante nao comeca com `ORCHESTRATOR_DECISION` ou equivalente claro;
- o pedido segue para implementacao sem `execucao`, `pipeline_selecionado`, `informacao_completa` e `lacunas` coerentes;
- um batch termina sem findings adversariais e sem status de validacao;
- a conclusao final nao informa checks executados e verdict.

## Regras de downgrade

Se o runtime nao conseguir provar enforcement, a documentacao deve usar `guided parity` ou `no parity`.

Nao e permitido:

- chamar instruction textual de enforcement nativo;
- chamar prompt manual de bloqueio automatico;
- chamar contract-only stage de agente operacional.

## Contracto minimo de emulacao

Para que um gate conte como emulado no Copilot, o artifact correspondente deve:

1. nomear explicitamente o gate;
2. descrever o comportamento esperado;
3. declarar a limitacao de runtime quando ela existir;
4. apontar a proxima acao ou handoff.

## Consequencia para sync e validator

O validator do mirror deve checar snippets que provem:

- existencia dos gates centrais;
- classificacao honesta de paridade;
- ausencia de claims de hook enforcement inexistente.