---
title: Superpowers gate emulation
area: superpowers-gates
tags:
  - superpowers
  - gates
  - enforcement
  - compatibility
  - instructions
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir como os gates centrais do workflow `superpowers` são preservados no GitHub Copilot quando o runtime não oferece enforcement equivalente ao Claude.

## Claims versus enforcement

| Gate | Objetivo | Enforcement no Copilot | Estratégia |
| --- | --- | --- | --- |
| brainstorming before planning | impedir execução precipitada | guided | prompts e coordinator devem devolver o usuário para brainstorming quando faltar framing |
| approved plan before execution | impedir código sem plano aprovado | guided | instruction always-on + prompts de execução exigem plano aprovado explícito |
| checkpoint por batch | impedir execução longa sem controle | guided | prompts de execução exigem status do batch e validação local |
| final verification before completion | impedir conclusão sem revisão final | guided | instruction + prompt final forçam veredito pass, warn ou fail |
| honest runtime claims | impedir “fidelidade falsa” | guided | knowledge base e coordinator exigem distinguir nativo, guiado e sem paridade |

## Gates que o runtime não enforce sozinho

No ambiente atual, o Copilot não oferece bloqueio duro equivalente ao sistema original para:

- aprovação obrigatória de plano por evento de ciclo de vida;
- interceptação automática de toda transição entre etapas;
- garantia de que o usuário sempre usará o prompt correto.

Por isso, esta camada deve ser tratada como **enforcement guiado**.

## Consequência operacional

Quando um gate for guiado:

- o agente deve dizer explicitamente que está barrando a próxima etapa por política do workflow;
- o próximo passo correto deve ser nomeado;
- o veredito final não pode omitir falta de evidência.

## Sinais de violação

Considere violação de gate quando houver:

- implementação iniciada sem plano aprovado;
- conclusão declarada sem verificação final;
- troca de etapa sem handoff explícito;
- claim de paridade total sem evidência de runtime.

## Artifact responsibilities

### Instructions

Responsáveis por lembrar e reforçar os gates em toda execução.

### Prompts

Responsáveis por tornar cada gate visível no ponto de entrada e saída das etapas.

### Coordinator agent

Responsável por barrar rotas ilegítimas e explicar o redirecionamento.

### Knowledge base

Responsável por manter a honestidade do contrato de enforcement.

## Exit criterion do batch

O Batch 5 só pode ser considerado pronto quando:

1. a camada always-on existe;
2. o ledger de claims versus enforcement está versionado;
3. a limitação de runtime está explícita;
4. a conclusão final depende de verificação declarada.