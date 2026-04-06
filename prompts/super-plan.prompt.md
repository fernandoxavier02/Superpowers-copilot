---
agent: agent
description: "Claude and Windsurf compatible alias for the superpowers planning stage"
---

# /super-plan

Use este alias quando quiser abrir a etapa de planning com o nome curto do plugin original.

## Equivalência

- comando compatível: `/super-plan`
- prompt canônico no mirror Copilot: `/superpowers-writing-plans`

## O que fazer

1. Executar a disciplina de planning do prompt canônico.
2. Converter a exploração aprovada em batches, riscos e rollback.
3. Pedir aprovação explícita antes de qualquer execução.
4. Encerrar com handoff explícito para `/superpowers-subagent-driven-development` ou `/superpowers-executing-plans` quando o plano estiver aprovado.

## Transparência

- sinalizar que este comando é um alias de compatibilidade;
- manter a saída operacional alinhada ao prompt canônico `/superpowers-writing-plans`.

## Next prompt esperado

- `/superpowers-writing-plans`
- `/superpowers-executing-plans`