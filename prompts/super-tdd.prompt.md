---
agent: agent
description: "Apply the superpowers TDD discipline for behavior-changing work"
---

# /super-tdd

Use este comando quando a mudança exigir teste antes de código.

## O que fazer

1. Entender o comportamento alvo.
2. Escrever o teste que falha.
3. Confirmar a falha.
4. Implementar o mínimo para passar.
5. Refatorar com segurança.
6. Promover a cobertura de regressão antes de concluir.

## Gates obrigatórios

- não escrever código de produção antes do teste;
- não pular o ciclo RED;
- não declarar sucesso sem evidência de teste;
- quando a implementação terminar, passar por `/superpowers-verification-before-completion`.

## Output esperado

```markdown
## Super TDD

**Target behavior:** [comportamento]
**RED status:** [failing test confirmado]
**GREEN status:** [mínimo para passar]
**Refactor status:** [feito|adiado]

### Next prompt
/superpowers-verification-before-completion
```