---
agent: agent
description: "Claude and Windsurf compatible alias for batch execution"
---

# /super-execute

Use este alias quando quiser a rota de execução em batches com o nome curto do plugin original.

## Equivalência

- comando compatível: `/super-execute`
- prompt canônico no mirror Copilot: `/superpowers-executing-plans`

## O que fazer

1. Confirmar que existe plano aprovado.
2. Executar apenas o batch atual com o menor diff suficiente.
3. Registrar validação local e riscos residuais.
4. Encerrar com handoff para `/superpowers-verification-before-completion` quando a implementação terminar.

## Transparência

- sinalizar que este comando é um alias de compatibilidade;
- manter a saída operacional alinhada ao prompt canônico `/superpowers-executing-plans`.

## Next prompt esperado

- `/superpowers-executing-plans`
- `/superpowers-verification-before-completion`