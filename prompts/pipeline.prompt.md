---
name: pipeline
agent: agent
description: "Run the guided pipeline-orchestrator flow in GitHub Copilot"
---

# /pipeline

Use este comando para seguir o espelho guiado do `pipeline-orchestrator` no GitHub Copilot.

## O que fazer

1. Ler `.githubcopilot/knowledge-base/20-pipeline-orchestrator-canonical-baseline.md`.
2. Ler `.githubcopilot/knowledge-base/21-pipeline-orchestrator-copilot-compatibility-matrix.md`.
3. Ler `.githubcopilot/knowledge-base/25-pipeline-orchestrator-routing-and-handoffs.md`.
4. Se ja existir `ORCHESTRATOR_DECISION`, validar coerencia do bloco antes de reemitir.
5. Emitir `ORCHESTRATOR_DECISION` apenas quando ele ainda nao existir ou estiver incoerente com a solicitacao atual.
6. Se `informacao_completa = false`, parar na classificacao, listar `lacunas`, fazer exatamente UMA pergunta de clarificacao por vez e nao avancar para roteamento de execucao ate a resposta do usuario.
7. Identificar a rota correta apenas quando `informacao_completa = true`:
   - resposta direta;
   - classificacao com lacunas;
   - execucao guiada em batches;
   - validacao final.
8. Quando a rota ja estiver clara, fazer handoff explicito para o prompt `superpowers-*` correspondente em vez de manter a execucao genericamente em `/pipeline`.
   - se `informacao_completa = false`, parar na classificacao, fazer uma pergunta de clarificacao e nao fazer handoff ainda
   - se `execucao = trivial`, responder diretamente sem sair do pipeline
   - se ainda nao existe plano aprovado para trabalho nao trivial, handoff padrao -> `/superpowers-writing-plans`
   - exploracao aberta -> `/superpowers-brainstorming`
   - bug ou regressao -> `/superpowers-debugging`
   - mudanca de comportamento, feature com risco de regressao ou fix que precise prova automatizada -> `/superpowers-tdd`
   - plano aprovado + execucao linear -> `/superpowers-executing-plans`
   - plano aprovado + execucao multiagente -> `/superpowers-subagent-driven-development`
   - batch implementado e pronto para auditoria -> `/superpowers-code-review`
   - verificacao final -> `/superpowers-verification-before-completion`
9. Se houver execucao por batch, repetir por batch:
   - o que foi feito;
   - findings adversariais;
   - correcoes aplicadas;
   - status de validacao.
10. Encerrar com proximo handoff explicito ou com verdict final estruturado.

## Regras

- nao tratar `quality-gate-router` e `pre-tester` como agentes operacionais comprovados do Copilot;
- nao esconder gaps de runtime;
- nao declarar conclusao sem um checkpoint estruturado de verificacao final, deixando claro que isso e `guided parity`, nao enforcement tecnico;
- quando a rota especializada ja estiver definida, preferir handoff para `superpowers` em vez de manter coordenacao redundante no `pipeline-orchestrator`;
- quando faltar informacao, perguntar uma coisa por vez em vez de despejar um questionario inteiro;
- quando nao houver plano aprovado para trabalho nao trivial, nao pular direto para execucao;
- quando houver mudanca de comportamento ou protecao de regressao relevante, preferir `/superpowers-tdd` antes de codigo;
- em cada batch implementado, registrar findings adversariais e correcoes aplicadas antes de avancar;
- quando a rota escolhida for `classificacao`, parar ali em vez de avancar artificialmente para batches.

## Output esperado

```markdown
ORCHESTRATOR_DECISION:
  ...

## Pipeline route
[classificacao | batches guiados | validacao final]

## Parity note
[native parity | guided parity | no parity]

## Next step
[prompt/agente/check final; preferir `superpowers-*` quando a rota especializada ja estiver definida]
```