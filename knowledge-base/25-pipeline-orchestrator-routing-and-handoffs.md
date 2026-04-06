---
title: Pipeline-orchestrator routing and handoffs
area: pipeline-orchestrator-routing
tags:
  - pipeline-orchestrator
  - routing
  - handoffs
  - prompts
  - agents
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir como o espelho Copilot roteia pedidos entre `ask`, `enforce`, `pipeline`, o agente coordenador e as etapas de validacao.

## Routing contract

O espelho usa um contrato em duas camadas:

1. prompt manual para entrar na cadeia certa;
2. agent coordenador para preservar ownership, handoff e batch discipline.

## Entry-point routing

| Entrada | Quando usar | Saida minima obrigatoria | Proximo handoff padrao |
| --- | --- | --- | --- |
| `/ask` | qualquer pedido que precise apenas classificacao e primeira resposta disciplinada | `ORCHESTRATOR_DECISION` | resposta direta ou `/pipeline` |
| `/enforce` | quando o usuario quer modo estrito, sem tolerancia a gate parcial | `ORCHESTRATOR_DECISION` completo | `Pipeline Orchestrator` ou `/pipeline` |
| `/pipeline` | quando a solicitacao precisa do workflow completo ou multi-etapas | `ORCHESTRATOR_DECISION` + rota escolhida | `Pipeline Orchestrator` |
| `Pipeline Orchestrator` agent | quando a coordenacao precisa escolher cadeia, batch e limits de parity | `ORCHESTRATOR_DECISION` + routing note | proximo prompt/agente/validacao |

## Ownership por dimensao

| Dimensao | Dono no mirror Copilot | Fonte semantica |
| --- | --- | --- |
| emissao de `ORCHESTRATOR_DECISION` | prompts + instruction + agent | `.windsurf/CONSTITUTION.md`, `.windsurf/skills/orchestrate-task/SKILL.md` |
| tipo/persona/severidade | `Pipeline Orchestrator` agent | `.windsurf/skills/orchestrate-task/SKILL.md` |
| complexidade | `Pipeline Orchestrator` agent, com classifier explicito na resposta | `.windsurf/agents/context-classifier.md` |
| selecao de pipeline | `Pipeline Orchestrator` agent | `.windsurf/agents/orchestrator-documenter.md` |
| batch review | `Pipeline Orchestrator` agent + instructions | `.windsurf/agents/adversarial-reviewer.md` |
| validacao final | prompt final ou resposta estruturada do coordenador | `.windsurf/agents/final-validator.md` |

## Handoff rules

### H1. Ask -> direct response

Se `execucao = trivial` e `informacao_completa = true`, o pedido pode ser respondido apos o bloco `ORCHESTRATOR_DECISION`.

### H2. Ask/Enforce -> Pipeline

Se `execucao = pipeline`, a saida deve apontar explicitamente para `/pipeline` ou para o `Pipeline Orchestrator` agent.

### H3. Pipeline -> batch execution

Quando o pedido exigir implementacao, o coordenador deve quebrar o trabalho em batches e repetir este contrato por batch:

1. o que foi feito;
2. findings adversariais;
3. correcoes aplicadas;
4. status de validacao.

### H3b. Pipeline -> superpowers execution stage

Quando a classificacao e a selecao de rota ja estiverem resolvidas, o `pipeline-orchestrator` deve fazer handoff explicito para o stage especializado do `superpowers` em vez de reter a execucao inteira no coordenador.

Para pedidos externos ao workflow, o ownership do `ORCHESTRATOR_DECISION` continua sendo do `pipeline-orchestrator`.

O `superpowers` nao substitui esse gate inicial; ele apenas consome a rota decidida ou, quando o usuario entrar direto em um prompt `superpowers-*`, opera no stage pedido sem reivindicar ownership retroativo sobre a classificacao canonica.

## Decision matrix para handoff

| Sinal principal no `ORCHESTRATOR_DECISION` | Handoff recomendado | Observacao |
| --- | --- | --- |
| `informacao_completa = false` | parar na classificacao | nao avancar para `superpowers` ate resolver lacunas |
| `execucao = trivial` | resposta direta | sem handoff para `superpowers` |
| `execucao = pipeline` + pedido exploratorio ou framing insuficiente | `/superpowers-brainstorming` | quando ainda falta transformar problema em framing utilizavel |
| `execucao = pipeline` + rota pede plano antes de codigo | `/superpowers-writing-plans` | quando ja ha classificacao, mas ainda falta batch plan acionavel |
| `tipo = Bug Fix` ou risco de regressao/causa raiz incerta | `/superpowers-debugging` | preferir quando o proximo passo e investigacao sistematica |
| `tipo = Feature` ou mudanca comportamental com regressao esperada | `/superpowers-tdd` | preferir quando a cobertura precisa vir antes do codigo |
| `execucao = pipeline` + plano aprovado e execucao linear suficiente | `/superpowers-executing-plans` | usar quando multiagente nao agrega materialmente |
| `execucao = pipeline` + isolamento de contexto util | `/superpowers-subagent-driven-development` | usar quando multiagente reduz risco ou aumenta confiabilidade |
| implementacao pronta para auditoria | `/superpowers-code-review` | antes de claim final ou entrega |
| execucao concluida e verificacao pendente | `/superpowers-verification-before-completion` | saida final especializada de verificacao |

Tabela de handoff recomendada:

| Rota decidida no pipeline | Destino preferencial em `superpowers` | Quando usar |
| --- | --- | --- |
| exploracao ainda aberta | `/superpowers-brainstorming` | quando faltam framing, restricoes ou criterios de pronto |
| plano necessario antes da execucao | `/superpowers-writing-plans` | quando a classificacao ja existe, mas ainda falta batch plan acionavel |
| bug, regressao ou falha intermitente | `/superpowers-debugging` | quando o problema exige causa raiz antes de implementar |
| mudanca de comportamento com protecao de regressao | `/superpowers-tdd` | quando o proprio pipeline indicar necessidade de teste antes do codigo |
| execucao linear de plano aprovado | `/superpowers-executing-plans` | quando nao ha ganho material em multiagente |
| execucao com multiagente util | `/superpowers-subagent-driven-development` | quando isolamento de contexto reduz risco ou aumenta confiabilidade |
| revisao tecnica antes de entrega | `/superpowers-code-review` | quando a implementacao ja existe e precisa de auditoria estruturada |
| verificacao final antes de concluir | `/superpowers-verification-before-completion` | obrigatorio antes de claim final quando houve execucao |

Esse handoff deve ser tratado como:

- ownership do `pipeline-orchestrator` para classificar e decidir a rota;
- ownership do `superpowers` para executar o workflow especializado depois da rota conhecida.

### H4. Pipeline -> final validation

No mirror Copilot, a conclusao final deve incluir um checkpoint estruturado de verificacao equivalente em intencao ao `FINAL_VALIDATOR_RESULT`, mas isso permanece `guided parity`.

Ou seja:

- o artifact deve exigir checks finais explicitos;
- a resposta final deve citar os checks executados e o verdict;
- o runtime do Copilot nao bloqueia tecnicamente a conclusao se o operador ignorar esse passo.

## Runtime state model

### Estado 1 - entrada nao classificada

Acao obrigatoria: emitir `ORCHESTRATOR_DECISION`.

### Estado 2 - classificada mas incompleta

Acao obrigatoria: marcar `informacao_completa: false` e preencher `lacunas`.

### Estado 3 - classificada e pronta para pipeline

Acao obrigatoria: escolher rota e declarar limite de paridade aplicavel.

Se a rota ja apontar para um stage especializado conhecido, o proximo passo deve citar explicitamente o prompt `superpowers-*` correspondente.

### Estado 4 - em execucao por batch

Acao obrigatoria: manter resumo, findings, correcoes e validacao por batch.

### Estado 5 - pronta para encerrar

Acao obrigatoria: emitir checks finais e verdict estruturado.

Em `guided parity`, isso significa disciplina textual e handoff correto, nao enforcement automatico por hook.

## Routing anti-patterns

Nao fazer:

- responder implementacao longa sem `ORCHESTRATOR_DECISION`;
- pular de `/ask` para conclusao sem dizer a rota escolhida;
- usar `/pipeline` para esconder que falta informacao;
- declarar que `quality-gate-router` ou `pre-tester` rodaram no Copilot sem evidencia operacional.

## Estado atual de runtime

No Copilot, o handoff e preservado por texto, prompts, instructions e agent design.

Isso significa:

- handoff explicito tem `guided parity`;
- handoff automatico por hook/plugin permanece fora da paridade nativa.
- `guided parity` aqui significa texto + disciplina operacional + prompts corretos, nunca bloqueio automatico de runtime.

## Integracao com superpowers

O `pipeline-orchestrator` e a camada de entrada disciplinada.

O `superpowers` e a camada de execucao especializada.

Logo:

- `pipeline-orchestrator` decide `ORCHESTRATOR_DECISION`, classifica e escolhe a rota;
- `superpowers` recebe a rota ja decidida e executa o workflow especializado;
- quando o usuario entrar direto em `superpowers-*`, o stage pode operar sem reemitir `ORCHESTRATOR_DECISION`, mas nao substitui a classificacao canonica do `pipeline-orchestrator` para requests externos ambiguos;
- nenhuma resposta deve sugerir que ambos possuem ownership simultaneo sobre classificacao ou gate inicial.