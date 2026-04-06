---
agent: agent
description: "Run the brainstorming stage of the superpowers chain"
---

# /superpowers-brainstorming

Execute a etapa de brainstorming do workflow `superpowers`.

## O que fazer

1. Delimitar objetivo, restrições, riscos e sinais de sucesso.
2. Separar fatos verificados, hipóteses e lacunas.
3. Identificar se o problema pede pesquisa adicional, decisão arquitetural ou decomposição operacional.
4. Produzir um resumo de exploração que deixe claro se já existe material suficiente para planejar.
5. Encerrar com handoff explícito para `/superpowers-writing-plans` quando a exploração estiver madura.

## Regras

- não iniciar implementação;
- não fingir que há plano aprovado nesta etapa;
- se faltarem dados críticos, pare e diga exatamente o que falta.

## Output esperado

```markdown
## Brainstorming

**Goal:** [objetivo]
**Constraints:** [restrições principais]
**Known facts:** [fatos verificados]
**Unknowns:** [lacunas]
**Recommended next step:** /superpowers-writing-plans
```