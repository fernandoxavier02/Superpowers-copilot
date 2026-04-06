---
agent: agent
description: "Claude and Windsurf compatible router for the superpowers command chain"
---

# /super

Use este comando quando quiser seguir a cadeia disciplinada do pacote `superpowers` com nomenclatura compatível com Claude/Windsurf.

## O que fazer

1. Classificar se o pedido pede exploração, planejamento, execução, debugging, TDD, verificação final ou review final.
2. Explicar em 2 a 4 linhas por que essa é a etapa correta.
3. Sinalizar explicitamente qual prompt superpowers será usado a seguir.
4. Encerrar com handoff explícito para exatamente um dos comandos abaixo.

## Comandos permitidos

- `/super-brainstorm`
- `/super-plan`
- `/super-subagent`
- `/super-execute`
- `/super-tdd`
- `/super-debug`
- `/super-verify`
- `/super-review`

## Gates obrigatórios

- não implementar antes de design aprovado quando houver lacunas materiais;
- não pular de brainstorming direto para execução;
- não executar plano sem aprovação explícita;
- não declarar conclusão sem `/super-verify` ou `/superpowers-verification-before-completion`.

## Output esperado

```markdown
## Super Router

**Current stage:** [brainstorm|plan|subagent|execute|tdd|debug|verify|review]
**Why:** [justificativa curta]
**Compatibility note:** [qual alias Claude/Windsurf será usado]

### Next prompt
/super-[etapa]
```