---
title: Pipeline-orchestrator packaging and discovery
area: pipeline-orchestrator-packaging
tags:
  - pipeline-orchestrator
  - prompts
  - agents
  - instructions
  - discovery
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Traduzir o `pipeline-orchestrator` em um conjunto de artifacts Copilot descobriveis, com o menor overlap possivel entre prompts, instructions, knowledge base e agent.

## Principio geral

O espelho precisa de duas superficies complementares:

- entry points manuais com nomes reais evidenciados no workspace original;
- uma camada coordenadora que mantenha classificacao, routing, handoffs e limites de paridade.

## Packaging map

| Capability | Artifact primario | Por que |
| --- | --- | --- |
| `/ask` | `.prompt.md` | entrada manual curta para classificacao obrigatoria |
| `/enforce` | `.prompt.md` | modo estrito para exigir os 12 campos e o gate inicial |
| `/pipeline` | `.prompt.md` + `.agent.md` | entry point manual com handoff para coordenacao quando necessario |
| coordenacao do espelho | `.agent.md` | ownership central de classificacao, routing e honestidade de paridade |
| gates transversais | `*.instructions.md` | guardrails always-on curtos e estaveis |
| baseline, limites e matrix | `knowledge-base/` | profundidade e evidencia de sustentacao |

## Entry points aprovados neste mirror

### Comandos evidenciados e replicados

- `/ask`
- `/enforce`
- `/pipeline`

### Comandos deliberadamente nao criados

- `/orchestrate`

Motivo: nao ha evidencia suficiente de que esse alias existe no sistema original observado.

## Artifacts aprovados do Batch 2

### Prompts

- `prompts/ask.prompt.md`
- `prompts/enforce.prompt.md`
- `prompts/pipeline.prompt.md`

### Agent

- `agents/pipeline-orchestrator.agent.md`

### Instruction

- `instructions/50-pipeline-orchestrator-gates.instructions.md`

### Knowledge base de suporte

- `knowledge-base/20-pipeline-orchestrator-canonical-baseline.md`
- `knowledge-base/21-pipeline-orchestrator-copilot-compatibility-matrix.md`
- `knowledge-base/23-pipeline-orchestrator-ssot-and-mirror-boundaries.md`
- `knowledge-base/24-pipeline-orchestrator-packaging-and-discovery.md`
- `knowledge-base/25-pipeline-orchestrator-routing-and-handoffs.md`
- `knowledge-base/26-pipeline-orchestrator-gate-emulation.md`

## Discovery contract

### Workspace discovery

O workspace ja possui discovery habilitado para:

- `chat.promptFilesLocations` -> `.githubcopilot/prompts`
- `chat.agentFilesLocations` -> `.githubcopilot/agents`
- `chat.instructionsFilesLocations` -> `.githubcopilot/instructions`

Logo, os artifacts deste batch entram no runtime local sem exigir nova pasta de discovery.

### Global discovery

No mirror global, os mesmos diretivos precisam apontar para:

- `~/.githubcopilot-global/prompts`
- `~/.githubcopilot-global/agents`
- `~/.githubcopilot-global/instructions`

Esse wiring sera validado nos batches de sync e activation.

## Anti-duplication rules

Nao fazer:

- prompt e instruction com o mesmo corpo operacional;
- prompt duplicado com nome longo e alias curto para a mesma funcao sem diferenca de papel;
- agent que repita a baseline inteira em vez de aponta-la como ordem de leitura.

Fazer:

- prompt para entrada manual;
- instruction para guardrail transversal;
- agent para coordenacao e handoff;
- KB para evidencia, matriz e limites.

## Consequencia para os proximos batches

O approved set do sync e do validator deve considerar estes artifacts como o nucleo operacional do espelho `pipeline-orchestrator`.