---
agent: agent
description: "Convert exploration into an approved execution plan"
---

# /superpowers-writing-plans

Execute a etapa `writing-plans` do workflow `superpowers`.

## O que fazer

1. Ler a baseline canônica e respeitar os invariantes do workflow.
2. Converter a exploração disponível em um plano executável com batches, entregáveis, riscos e rollback.
3. Distinguir claramente:
   - o que está confirmado;
   - o que depende de validação;
   - o que bloqueia execução.
4. Pedir aprovação explícita do plano antes de qualquer execução.
5. Encerrar com handoff explícito para um dos caminhos de execução apenas se o plano estiver aprovado:
   - `/superpowers-subagent-driven-development`
   - `/superpowers-executing-plans`

## Regras

- não começar implementação antes da aprovação explícita do plano;
- preferir batches pequenos e verificáveis;
- registrar limitações de runtime do Copilot quando afetarem o plano.

## Output esperado

```markdown
## Writing Plans

**Plan status:** [draft|approved|blocked]
**Execution path:** [subagent-driven-development|executing-plans|not-ready]

### Batches
1. [batch 1]
2. [batch 2]

### Approval needed
[sim ou nao, com motivo]

### Next prompt
/superpowers-[execution-path]
```