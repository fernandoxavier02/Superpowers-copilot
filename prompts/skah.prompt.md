---
agent: agent
description: "Short alias for the agent-customization workflow in VS Code and GitHub Copilot"
---

# /skah

Use este alias quando quiser criar, revisar, corrigir ou depurar arquivos de customizacao de agente com um comando curto do workspace.

## Equivalencia

- comando compatível: `/skah`
- workflow alvo: `agent-customization`

## O que fazer

1. Confirmar que o pedido envolve arquivos de customizacao como `.instructions.md`, `.prompt.md`, `.agent.md`, `SKILL.md`, `copilot-instructions.md` ou `AGENTS.md`.
2. Se for uma operacao simples em um unico arquivo conhecido, executar direto com diff minimo.
3. Se for criacao, refatoracao, troubleshooting de discovery ou definicao de primitiva, seguir a disciplina da workflow skill `agent-customization`.
4. Escolher explicitamente entre prompt, instruction, agent, skill, hook ou workspace instruction quando isso fizer diferenca no resultado.
5. Encerrar com o artefato criado ou editado, ou com o proximo passo objetivo quando faltar contexto.

## Transparencia

- sinalizar que este comando e um alias curto para o workflow de customization;
- nao tratar perguntas gerais de codigo como escopo deste comando;
- manter a resposta focada em customizacao de agentes, prompts, instructions, skills e discovery.

## Next step esperado

- editar o arquivo de customizacao correto;
- criar o artefato de customizacao correto;
- pedir apenas o contexto minimo faltante quando a primitiva ainda nao estiver clara.