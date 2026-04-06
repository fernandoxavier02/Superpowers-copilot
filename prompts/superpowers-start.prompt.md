---
agent: agent
description: "Start the superpowers chain and route to the correct first stage"
---

# /superpowers-start

Inicie o workflow `superpowers` no GitHub Copilot sem pular etapas.

## O que fazer

1. Ler a baseline canônica em `.githubcopilot/knowledge-base/10-superpowers-canonical-baseline.md`.
2. Ler a matriz de compatibilidade em `.githubcopilot/knowledge-base/11-superpowers-copilot-compatibility-matrix.md`.
3. Classificar a solicitação do usuário em uma destas opções:
   - precisa de brainstorming primeiro;
   - já tem exploração suficiente e precisa de plano;
   - já tem plano aprovado e precisa de execução;
   - já executou e precisa de verificação final.
4. Explicar em 2 a 4 linhas por que esta é a etapa correta de entrada.
5. Encerrar com handoff explícito para exatamente um destes prompts:
   - `/superpowers-brainstorming`
   - `/superpowers-writing-plans`
   - `/superpowers-subagent-driven-development`
   - `/superpowers-executing-plans`
   - `/superpowers-verification-before-completion`

## Regras

- não mandar o usuário para execução se ainda faltar plano aprovado;
- não declarar equivalência de runtime onde a matriz marcou compatibilidade guiada;
- se houver ambiguidade relevante, pedir o mínimo de clarificação necessário antes de escolher a etapa.

## Output esperado

```markdown
## Superpowers Entry

**Current stage:** [brainstorming|writing-plans|subagent-driven-development|executing-plans|verification-before-completion]
**Why:** [justificativa curta]

### Next prompt
/superpowers-[etapa]
```