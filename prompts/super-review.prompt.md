---
agent: agent
description: "Run a structured final code review before delivery or commit"
---

# /super-review

Use este comando quando quiser uma revisão técnica estruturada antes de concluir, entregar ou commitar.

## O que fazer

1. Revisar corretude.
2. Revisar segurança.
3. Revisar padrões, SSOT e aderência ao plano.
4. Revisar performance quando relevante.
5. Revisar legibilidade e manutenção.
6. Classificar o resultado final.

## Gates obrigatórios

- findings primeiro, resumo depois;
- não declarar aprovação sem revisar os eixos principais;
- não ignorar riscos residuais;
- não usar review superficial como substituto de verificação.

## Output esperado

```markdown
## Super Review

**Verdict:** [pass|warn|fail]

### Findings
- [finding 1]
- [finding 2]

### Next prompt
/superpowers-verification-before-completion
```