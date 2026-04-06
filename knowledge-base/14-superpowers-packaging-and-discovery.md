---
title: Superpowers packaging and discovery
area: superpowers-packaging
tags:
  - superpowers
  - prompts
  - skills
  - agents
  - discovery
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Traduzir a cadeia `superpowers` em um conjunto de artifacts Copilot descobríveis, com o menor overlap possível entre prompts, skills, instructions e agents.

## Princípio geral

O espelho precisa ter duas superfícies complementares:

- **entry points manuais** para o usuário entrar conscientemente na cadeia;
- **capabilities de suporte** para coordenação, governança e reutilização.

## Packaging map

| Capability | Artifact primário | Por quê |
| --- | --- | --- |
| entrada do workflow `superpowers` | `.prompt.md` | o usuário precisa de slash commands claros para iniciar a cadeia |
| brainstorming | `.prompt.md` | é uma etapa manual e nomeada do workflow |
| writing-plans | `.prompt.md` | mantém a transição manual e explícita para planejamento |
| execution path selection | `.prompt.md` + `.agent.md` | prompt inicia; agente coordenador decide a melhor rota quando necessário |
| subagent-driven-development | `.prompt.md` + `.agent.md` | prompt como entry point; agent team quando coordenação real for útil |
| executing-plans | `.prompt.md` | caminho manual direto de execução em batches |
| verification-before-completion | `.prompt.md` | gate final precisa ser altamente descobrível |
| regras transversais de qualidade | `*.instructions.md` | são guardrails always-on, não workflows |
| pesquisa, curadoria, sync e readiness | `SKILL.md` | são capacidades reutilizáveis e não etapas centrais do fluxo |

## Entry points mínimos obrigatórios

O espelho Copilot deve expor pelo menos:

- `/superpowers-start`
- `/superpowers-brainstorming`
- `/superpowers-writing-plans`
- `/superpowers-subagent-driven-development`
- `/superpowers-executing-plans`
- `/superpowers-verification-before-completion`

## Aliases de compatibilidade Claude/Windsurf

Para reduzir atrito de migração, o espelho também deve expor aliases curtos compatíveis com os comandos do plugin original:

- `/super`
- `/super-brainstorm`
- `/super-plan`
- `/super-subagent`
- `/super-execute`
- `/super-verify`
- `/super-tdd`
- `/super-debug`
- `/super-review`

## Regras de discovery

### Prompts

- precisam viver em pasta incluída por `chat.promptFilesLocations`;
- devem ter nomes previsíveis e alinhados ao nome da etapa canônica;
- devem terminar com handoff explícito para a próxima etapa.

### Agents

- precisam viver em pasta incluída por `chat.agentFilesLocations`;
- o coordenador deve ser visível;
- workers de execução ou revisão podem ficar não user-invocable quando o fluxo estiver maduro.

### Skills

- precisam viver em pasta incluída por `chat.agentSkillsLocations`;
- devem encapsular capacidades duráveis, não duplicar prompts etapa por etapa.

### Instructions

- devem permanecer curtas e estáveis;
- não devem tentar duplicar o texto operacional dos prompts.

## Naming convention

### Prompts do workflow canônico

- `superpowers-start.prompt.md`
- `superpowers-brainstorming.prompt.md`
- `superpowers-writing-plans.prompt.md`
- `superpowers-subagent-driven-development.prompt.md`
- `superpowers-executing-plans.prompt.md`
- `superpowers-verification-before-completion.prompt.md`

### Regras de nome

- manter o nome canônico da etapa quando existir;
- usar prefixo `superpowers-` em todos os entry points centrais;
- usar aliases `super-*` apenas como camada explícita de compatibilidade com Claude/Windsurf.

## Anti-duplication rules

Não fazer:

- uma skill para cada etapa central e um prompt idêntico para a mesma etapa;
- uma instruction longa repetindo o corpo do prompt;
- múltiplos prompts diferentes para o mesmo gate sem justificativa.

Fazer:

- prompt para entrada;
- instruction para guardrail transversal;
- skill para capability auxiliar;
- agent para coordenação quando houver decisão real ou multiagente.

## Discovery checklist

Antes de considerar o Batch 3 concluído em runtime:

1. `chat.promptFilesLocations` inclui a pasta de prompts do workspace ou global;
2. `chat.agentFilesLocations` inclui `.githubcopilot/agents/`;
3. `chat.agentSkillsLocations` inclui `.githubcopilot/skills/` quando necessário;
4. os nomes dos prompts aparecem de forma previsível no slash command picker;
5. o usuário consegue inferir a ordem da cadeia pelos nomes.

## Boundary with later batches

Este batch decide **onde** cada capability mora e **como** o usuário a encontra.

Ele não decide ainda:

- toda a lógica de roteamento do coordenador;
- todas as restrições de gate em runtime;
- a política final de sincronização global.