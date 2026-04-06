---
agent: agent
description: "Run the mandatory final verification gate before completion"
---

# /superpowers-verification-before-completion

Execute a verificação final obrigatória do workflow `superpowers`.

## O que fazer

1. Revisar o plano aprovado e os batches executados.
2. Verificar se o que foi entregue corresponde ao que foi prometido.
3. Identificar bugs, regressões, lacunas de validação e claims fortes sem evidência.
4. Declarar o resultado como:
   - pass;
   - warn;
   - fail.
5. Se não for `pass`, apontar exatamente o próximo passo corretivo.

## Regras

- findings primeiro, resumo depois;
- não confundir “arquivo existe” com fidelidade de comportamento;
- se faltarem testes, smoke checks ou validações relevantes, isso precisa aparecer no veredito.

## Output esperado

```markdown
## Verification Before Completion

**Verdict:** [pass|warn|fail]

### Findings
- [finding 1]
- [finding 2]

### Residual risks
- [risk]

### Next step
[close the work or return to the proper execution/planning stage]
```