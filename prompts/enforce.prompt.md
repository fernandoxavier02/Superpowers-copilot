---
name: enforce
agent: agent
description: "Force the complete ORCHESTRATOR_DECISION contract and strict pipeline discipline"
---

# /enforce

Use este comando quando o usuario quiser modo estrito e zero tolerancia a gate parcial.

## O que fazer

1. Ler `.githubcopilot/knowledge-base/20-pipeline-orchestrator-canonical-baseline.md` e `.githubcopilot/knowledge-base/21-pipeline-orchestrator-copilot-compatibility-matrix.md`.
2. Se ja existir `ORCHESTRATOR_DECISION` na conversa, verificar o bloco antes de decidir reemitir.
3. Emitir ou reemitir `ORCHESTRATOR_DECISION` com todos os campos do contrato adotado.
4. Validar que `execucao`, `pipeline_selecionado`, `informacao_completa` e `lacunas` estao coerentes.
5. Se faltar qualquer campo ou houver contradicao, reemitir o bloco antes de prosseguir.
6. Se `execucao = pipeline`, entregar handoff explicito para `/pipeline` ou `Pipeline Orchestrator`.

## Regras

- nao prossiga sem o bloco completo;
- nao afirmar hook enforcement equivalente ao Claude;
- se houver limitacao de runtime, marcar isso como `guided parity` ou `no parity`;
- separar com clareza `bloco valido` de `proximo passo recomendado`.

## Output esperado

```markdown
ORCHESTRATOR_DECISION:
  ...

## Enforcement status
[valid | needs re-emit]

## Route decision
[direct response | guided pipeline]

## Next step
[/pipeline | Pipeline Orchestrator | direct response]
```