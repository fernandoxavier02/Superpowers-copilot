---
agent: agent
description: "Run the superpowers debugging flow for bugs, regressions and intermittent failures"
---

# /super-debug

Use este comando quando houver bug, regressão, falha intermitente ou comportamento inesperado.

## O que fazer

1. Reproduzir o problema.
2. Isolar o escopo.
3. Formular hipóteses.
4. Testar hipóteses.
5. Confirmar a causa raiz.
6. Corrigir minimamente.
7. Validar o resultado.

## Gates obrigatórios

- não adivinhar causa raiz;
- não mascarar sintomas;
- não corrigir antes de entender o problema;
- quando a correção exigir proteção de regressão, encaminhar para `/super-tdd`.

## Output esperado

```markdown
## Super Debug

**Reproduction:** [como reproduzir]
**Scope isolated:** [escopo]
**Root cause:** [causa raiz confirmada]
**Fix strategy:** [correção mínima]

### Next prompt
/super-tdd
ou
/superpowers-verification-before-completion
```