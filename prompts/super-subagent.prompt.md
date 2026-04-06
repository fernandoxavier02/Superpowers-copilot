---
agent: agent
description: "Claude and Windsurf compatible alias for same-session subagent execution"
---

# /super-subagent

Use este alias quando quiser a rota de execução multiagente na mesma sessão com o nome curto do plugin original.

## Equivalência

- comando compatível: `/super-subagent`
- prompt canônico no mirror Copilot: `/superpowers-subagent-driven-development`

## O que fazer

1. Confirmar que existe plano aprovado.
2. Executar batches pequenos com coordenação seletiva de agentes.
3. Fazer handoff para `/superpowers-executing-plans` quando ainda houver batches restantes.
4. Encerrar com handoff para `/superpowers-verification-before-completion` ao final da implementação.

## Transparência

- sinalizar que este comando é um alias de compatibilidade;
- manter a saída operacional alinhada ao prompt canônico `/superpowers-subagent-driven-development`.

## Next prompt esperado

- `/superpowers-subagent-driven-development`
- `/superpowers-verification-before-completion`