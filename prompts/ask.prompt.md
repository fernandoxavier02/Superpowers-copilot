---
name: ask
agent: agent
description: "Force ORCHESTRATOR_DECISION classification before answering"
---

# /ask

Use este comando quando precisar classificar a solicitacao primeiro, antes de qualquer analise detalhada ou implementacao.

## O que fazer

1. Ler `.githubcopilot/knowledge-base/20-pipeline-orchestrator-canonical-baseline.md`.
2. Se ja existir `ORCHESTRATOR_DECISION` valido na conversa atual, reaproveitar o bloco e corrigir apenas campos incoerentes.
3. Caso contrario, emitir `ORCHESTRATOR_DECISION` usando o shape semantico adotado no mirror.
4. Se `informacao_completa = false`, listar `lacunas` e parar na classificacao.
5. Se `execucao = trivial`, responder apos o bloco YAML.
6. Se `execucao = pipeline`, encerrar com handoff explicito para `/pipeline` ou para o agente `Pipeline Orchestrator`.

## Regras

- nao pular `ORCHESTRATOR_DECISION`;
- nao inventar lacunas;
- nao prometer enforcement nativo do runtime;
- ser explicito sobre a rota escolhida;
- preferir a menor rota suficiente: resposta direta quando trivial, pipeline quando houver multi-etapas, batching ou gates finais.

## Output esperado

```markdown
ORCHESTRATOR_DECISION:
  ...

## Route decision
[trivial direct response | guided pipeline]

## Next step
[direct response | /pipeline | Pipeline Orchestrator]
```