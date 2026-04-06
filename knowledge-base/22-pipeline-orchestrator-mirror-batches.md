---
title: Pipeline-orchestrator mirror batches
area: pipeline-orchestrator-batches
tags:
  - pipeline-orchestrator
  - github-copilot
  - batches
  - adversarial-review
  - rollout
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Traduzir o espelhamento `pipeline-orchestrator -> GitHub Copilot` em batches executaveis, com entregaveis, revisao adversarial e criterios de pronto.

## Regra de execucao

Nenhum batch avanca sem:

- entregavel explicito;
- revisao adversarial do proprio batch;
- ledger de gaps residuais;
- validacao correspondente ao escopo do batch;
- downgrade explicito de claims exageradas.

## Batches aprovados

### Batch 0 - Discovery e baseline real

**Objetivo**
Congelar a evidencia do pipeline original antes de adaptar qualquer artifact do Copilot.

**Entregaveis**
- baseline de fontes reais;
- cadeia operacional observada;
- conflito de SSOT e decisao de autoridade para o mirror.

**Findings adversariais minimos**
- `ORCHESTRATOR_DECISION` possui duplicidade real de formato;
- `ORCHESTRATOR_DOC_RESULT` versus `ORCHESTRATOR_PIPELINE_DECISION` revela drift de naming;
- `quality-gate-router` e `pre-tester` sao descritos, mas nao estao materializados como agentes locais em `.windsurf/agents/`.

**Done when**
- o mirror sabe exatamente o que e semantic source, o que e reference mirror e o que e plugin-only.

### Batch 1 - Canonical baseline + compatibility matrix + SSOT boundaries

**Objetivo**
Documentar baseline, matriz e fronteiras de autoridade antes de qualquer artifact operacional.

**Entregaveis**
- canonical baseline;
- compatibility matrix;
- mirror batches ledger;
- SSOT and mirror boundaries.

**Done when**
- a autoridade para cada capability esta explicita;
- o ledger de paridade esta honesto;
- o conflito de SSOT esta documentado, nao oculto.

### Batch 2 - Packaging, routing, gates e agente coordenador

**Objetivo**
Criar a camada operacional do mirror em `.githubcopilot/`.

**Entregaveis**
- prompts de entrada e aliases evidenciados;
- instructions always-on;
- agente coordenador do pipeline;
- docs de packaging, routing e gate emulation.

**Adversarial review obrigatoria**
- verificar duplicacao entre prompts, instructions e agent;
- cortar aliases sem evidencia real;
- remover qualquer claim de enforcement nativo inexistente.

### Batch 3 - Sync, approved set e validation harness

**Objetivo**
Definir o conjunto aprovado do mirror e a promocao workspace -> global.

**Entregaveis**
- script de sync dedicado;
- validator dedicado;
- approved file set;
- suporte a `workspace-only`, `--require-global` e `--user-settings`.

**Done when**
- o preflight bloqueia sync quebrado;
- o mirror global pode ser validado sem inspecao manual;
- settings do usuario podem ser auditados quando exigidos.

### Batch 4 - Discovery real e ativacao global

**Objetivo**
Conectar o mirror aos mecanismos reais de discovery do VS Code.

**Entregaveis**
- atualizacoes necessarias em `.vscode/settings.json`;
- docs de ativacao global;
- sync para `~/.githubcopilot-global/`;
- validacao end-to-end de workspace, global e settings.

### Batch 5 - Adversarial cleanup e command parity final

**Objetivo**
Pressionar o mirror contra overclaim, drift e coverage insuficiente.

**Entregaveis**
- revisao final de claims de paridade;
- revisao final de prompts/aliases;
- correcoes de drift entre docs, sync e validator.

**Done when**
- a documentacao operacional bate com o runtime suportado;
- o espelho nao promete o que nao consegue provar.

### Batch 6 - Final validation

**Objetivo**
Rodar todas as validacoes e consolidar o estado final do mirror.

**Entregaveis**
- resultado dos validators;
- inventario final de arquivos;
- matriz final de native/guided/no parity;
- lista de limitacoes remanescentes e fora de escopo.

## Matriz obrigatoria de revisao adversarial

### Eixo 1 - Estrutural

- os arquivos estao no lugar certo para discovery do VS Code;
- o approved set bate com sync e validator;
- o indice e os catalogos apontam para os arquivos reais.

### Eixo 2 - Comportamental

- o entry gate `ORCHESTRATOR_DECISION` continua visivel;
- handoffs estao explicitos;
- batch review e final validation nao foram pulados;
- limites de paridade aparecem onde o runtime nao enforce.

### Eixo 3 - Operacional

- existe preflight de workspace;
- existe validacao de global mirror quando exigido;
- existe validacao de settings do usuario;
- rollback e drift handling continuam simples.

## Gate de honestidade

Todo batch precisa responder explicitamente:

- o que ficou com `native parity`;
- o que ficou com `guided parity`;
- o que ficou com `no parity`.

Sem esse ledger, o batch nao esta pronto.