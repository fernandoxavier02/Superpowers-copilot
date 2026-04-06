---
title: Superpowers mirror batches
area: superpowers-batches
tags:
  - superpowers
  - github-copilot
  - batches
  - adversarial-review
  - rollout
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Traduzir o plano de espelhamento `superpowers -> GitHub Copilot` em batches executáveis, com entregáveis, revisão adversarial e critérios de pronto.

## Regra de execução

Nenhum batch avança sem:

- entregável explícito;
- revisão adversarial em três eixos;
- ledger de gaps residuais;
- rollback simples.

## Batches

### Batch 0 — Canonical baseline

**Objetivo**
Congelar a baseline canônica do sistema original.

**Entregáveis**
- baseline canônica;
- activation order;
- invariants ledger.

**Arquivos-alvo**
- `knowledge-base/10-superpowers-canonical-baseline.md`

**Done when**
- a cadeia mínima está explícita;
- os handoffs obrigatórios estão listados;
- os invariantes de fidelidade estão congelados.

### Batch 1 — Compatibility matrix

**Objetivo**
Mapear capability por capability para o runtime Copilot.

**Entregáveis**
- matriz de compatibilidade;
- residual-gap register.

**Arquivos-alvo**
- `knowledge-base/11-superpowers-copilot-compatibility-matrix.md`

**Done when**
- cada capability crítica tem target artifact e classificação de paridade;
- os gaps irredutíveis estão explícitos.

### Batch 2 — SSOT and mirror boundaries

**Objetivo**
Congelar fronteiras entre fonte de verdade, adaptação Copilot e mirror global.

**Entregáveis**
- política de SSOT;
- política de drift;
- política de promoção workspace -> global.

**Done when**
- o que é local e o que é global está fechado;
- não há autoridade duplicada entre `.claude/`, `.githubcopilot/` e mirror global.

### Batch 3 — Packaging and discovery

**Objetivo**
Definir o mapa de artifacts Copilot do espelho.

**Entregáveis**
- artifact map;
- convenção de naming;
- contrato de discovery.

**Done when**
- cada capability principal sabe se vira instruction, prompt, skill ou agent;
- o conjunto resultante continua descobrível e não redundante.

### Batch 4 — Orchestrator and routing

**Objetivo**
Adaptar a cadeia de handoff para a topologia Copilot.

**Entregáveis**
- routing diagram;
- handoff contracts;
- entry/exit rules por etapa.

**Done when**
- o caminho planning -> execution -> verification está claro;
- o coordenador e os workers têm fronteiras de responsabilidade.

### Batch 5 — Gate emulation layer

**Objetivo**
Projetar a camada de compatibilidade para gates ausentes.

**Entregáveis**
- gate-emulation design;
- claims-vs-enforcement ledger.

**Done when**
- todo gate importante está marcado como nativo ou guiado;
- nenhum claim de enforcement fica implícito.

### Batch 6 — Specialist-agent integration

**Objetivo**
Integrar os agentes `.githubcopilot/agents/` ao espelho como equipe oficial.

**Entregáveis**
- team topology;
- KB usage contract;
- discovery prerequisites.

**Done when**
- o coordenador e os workers estão definidos;
- a dependência da knowledge base está explícita;
- ficou claro o que precisa ser configurado no VS Code para discovery real.

### Batch 7 — Workspace/global sync

**Objetivo**
Definir o espelho runtime para `~/.githubcopilot-global/`.

**Entregáveis**
- drift table;
- sync actions;
- promotion policy.

**Done when**
- existe política clara de promoção do workspace para o mirror global;
- drift e rollback estão cobertos.

### Batch 8 — Fidelity validation harness

**Objetivo**
Criar o conjunto de checks automáticos do espelho Copilot.

**Entregáveis**
- validation harness design;
- smoke checklist de chaining.

**Done when**
- a validação cobre estrutura e comportamento essencial;
- o harness distingue existência de arquivo de fidelidade real.

### Batch 9 — Rollout and hardening

**Objetivo**
Promover com segurança primeiro no workspace e depois no mirror global.

**Entregáveis**
- adoption readiness packet;
- known limitations report;
- rollback instructions.

**Done when**
- smoke tests passam no workspace;
- limitações conhecidas estão publicadas;
- o mirror global é promovido com critério explícito.

## Matriz obrigatória de revisão adversarial

### Eixo 1 — Estrutural

Perguntas mínimas:

- os artifacts estão no lugar certo para discovery do VS Code;
- há duplicação entre instructions, skills, prompts e agents;
- naming e frontmatter estão coerentes.

### Eixo 2 — Comportamental

Perguntas mínimas:

- a cadeia canônica ainda existe;
- os handoffs continuam explícitos;
- o usuário consegue pular gates sem fricção indevida;
- a verificação final continua obrigatória.

### Eixo 3 — Operacional

Perguntas mínimas:

- a instalação workspace/global é entendível;
- existe drift handling;
- há rollback simples;
- a invocabilidade dos agentes e skills foi realmente testada.

## Gate de honestidade

Todo batch precisa responder explicitamente:

- o que ficou com paridade nativa;
- o que ficou com compatibilidade guiada;
- o que continua sem paridade aceitável.

Se essa resposta não existir, o batch não está pronto.

## Próximo passo esperado

Após estes batches documentais iniciais, o próximo incremento de implementação deve começar pelo Batch 2: SSOT and mirror boundaries.