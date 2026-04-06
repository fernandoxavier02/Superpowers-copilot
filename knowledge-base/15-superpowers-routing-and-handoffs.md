---
title: Superpowers routing and handoffs
area: superpowers-routing
tags:
  - superpowers
  - routing
  - handoffs
  - orchestrator
  - agents
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir como o workflow `superpowers` entra, progride, escala e encerra no runtime do GitHub Copilot.

## Topologia mínima

### Entry surface

Prompts em `.githubcopilot/prompts/` funcionam como pontos de entrada explícitos do usuário.

### Coordinator surface

O agente `Superpowers Orchestrator` funciona como coordenador especializado quando o fluxo exigir decisão de rota, disciplina de handoff ou uso seletivo de agentes auxiliares.

### Support surfaces

- `instructions/` mantêm guardrails always-on;
- `skills/` suportam pesquisa, curadoria, sync e readiness;
- `knowledge-base/` sustenta contexto canônico e compatibilidade.

## Routing contract

| Current state | Próximo destino permitido | Condição |
| --- | --- | --- |
| pedido bruto ou ambíguo | `/superpowers-start` | quando ainda não está claro em que estágio o usuário está |
| problema exploratório | `/superpowers-brainstorming` | quando faltam framing, restrições ou critérios claros |
| exploração suficiente | `/superpowers-writing-plans` | quando já há insumo para virar batches executáveis |
| plano aprovado + coordenação multiagente útil | `/superpowers-subagent-driven-development` | quando isolamento de contexto ou papéis especializados reduz risco |
| plano aprovado + execução linear suficiente | `/superpowers-executing-plans` | quando não há ganho real em multiagente |
| implementação concluída | `/superpowers-verification-before-completion` | obrigatório antes de claim final |

## Forbidden routing

Rotas proibidas:

- brainstorming -> execução sem `writing-plans`;
- start -> execução quando o plano ainda não foi aprovado;
- qualquer execução -> conclusão final sem `verification-before-completion`.

## Handoff contract por etapa

### `/superpowers-start`

**Entrada**
- pedido do usuário ainda sem estágio claro.

**Saída obrigatória**
- um único próximo prompt recomendado;
- justificativa curta da escolha.

### `/superpowers-brainstorming`

**Entrada**
- objetivo ainda mal delimitado ou lacunas materiais de entendimento.

**Saída obrigatória**
- fatos, lacunas e recomendação explícita de ida para planning ou bloqueio por falta de informação.

### `/superpowers-writing-plans`

**Entrada**
- exploração suficiente para virar plano.

**Saída obrigatória**
- batches, riscos, rollback e status `draft`, `approved` ou `blocked`.

### `/superpowers-subagent-driven-development`

**Entrada**
- plano aprovado e motivo concreto para coordenação com agentes auxiliares.

**Saída obrigatória**
- batch executado;
- justificativa do uso de agentes;
- status de verificação local;
- handoff para verificação final.

### `/superpowers-executing-plans`

**Entrada**
- plano aprovado e execução linear suficiente.

**Saída obrigatória**
- batch executado;
- checkpoint de validação;
- handoff para verificação final.

### `/superpowers-verification-before-completion`

**Entrada**
- execução finalizada ou candidata a conclusão.

**Saída obrigatória**
- veredito `pass`, `warn` ou `fail`;
- findings primeiro;
- próximo passo corretivo quando necessário.

## Escalonamento para agentes auxiliares

O coordenador pode recomendar agentes auxiliares apenas quando o ganho for concreto.

No runtime atual, os agentes locais em `.githubcopilot/agents/` devem ser tratados como alvo de discovery do workspace. Para delegação garantidamente disponível agora, prefira os agentes já reconhecidos pelo ambiente.

### Casos válidos

- `Explore` para leitura paralela e mapeamento rápido do workspace;
- `agent` para execução geral quando a tarefa já estiver clara;
- agentes locais de `.githubcopilot/agents/` apenas quando a discovery do workspace estiver efetivamente habilitada.

### Casos inválidos

- usar multiagente sem redução real de risco;
- invocar agentes para tarefas triviais de edição;
- escalar revisão antes de existir delta implementado.

## Stop conditions do coordenador

Parar e devolver ao usuário quando:

- o plano não estiver aprovado e a rota pedida for execução;
- houver conflito entre baseline canônica e implementação pretendida;
- discovery/settings impedirem invocação confiável dos artifacts;
- a verificação final encontrar falhas materiais.

## Estado atual de runtime

Hoje, o espelho já tem prompts e um agente coordenador versionados no workspace, mas a delegação por nome para agentes locais ainda depende de discovery real do VS Code. Portanto:

- prompts locais já representam a superfície mais confiável de entrada;
- o coordenador documenta o fluxo e pode operar sem delegação local obrigatória;
- roteamento para agentes locais continua sendo capability alvo, não garantia operacional universal.

## Batch boundary

Este batch fecha o contrato de roteamento e handoff.

O próximo batch deve cuidar da camada de emulação de gates e claims-vs-enforcement.