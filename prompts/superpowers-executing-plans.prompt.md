---
agent: agent
description: "Execute an approved plan in controlled batches"
---

# /superpowers-executing-plans

Execute o plano aprovado pelo caminho `executing-plans`.

## O que fazer

1. Confirmar que existe plano aprovado.
2. Executar apenas o batch atual com o menor diff suficiente.
3. Validar o que foi alterado antes de avançar para o próximo batch.
4. Registrar bloqueios, riscos residuais e eventuais desvios do plano.
5. Encerrar com handoff para `/superpowers-verification-before-completion` ao final da implementação.

## Regras

- se não houver plano aprovado, parar e devolver para `/superpowers-writing-plans`;
- não acumular múltiplos batches sem checkpoint claro;
- não declarar conclusão final sem passar pelo prompt de verificação.

## Output esperado

```markdown
## Executing Plans

**Plan approval:** confirmed
**Batch status:** [in-progress|completed|blocked]
**Changes applied:** [resumo curto]
**Validation:** [resumo curto]

### Next prompt
/superpowers-verification-before-completion
```