---
agent: agent
description: "Execute an approved plan through coordinator plus workers"
---

# /superpowers-subagent-driven-development

Execute o plano aprovado pelo caminho `subagent-driven-development`.

## O que fazer

1. Confirmar que existe plano aprovado.
2. Quebrar a execução em batches pequenos.
3. Usar coordenação com agentes especializados quando isso realmente reduzir risco, contexto ou ambiguidade.
4. Após cada batch, registrar:
   - o que mudou;
   - o que foi verificado;
   - o que continua aberto.
5. Quando ainda houver batches aprovados para executar, fazer handoff explícito para `/superpowers-executing-plans`.
6. Encerrar com handoff para `/superpowers-verification-before-completion` quando a implementação terminar.

## Regras

- se não houver plano aprovado, parar e devolver para `/superpowers-writing-plans`;
- não usar multiagente por ornamentação;
- manter diffs mínimos e reversíveis;
- usar `/superpowers-executing-plans` como próximo prompt quando a execução ainda precisar continuar;
- parar quando houver bloqueio material ou falha repetida de validação.

## Output esperado

```markdown
## Subagent-Driven Development

**Plan approval:** confirmed
**Current batch:** [nome]
**Coordinator decision:** [por que usar ou nao subagentes]
**Verification status:** [pass|warn|blocked]

### Next prompt
/superpowers-executing-plans
ou
/superpowers-verification-before-completion
```