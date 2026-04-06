---
title: Superpowers SSOT and mirror boundaries
area: superpowers-ssot
tags:
  - superpowers
  - ssot
  - github-copilot
  - mirror
  - governance
globs:
  - "**"
updated: 2026-03-24
---

## Objetivo

Definir fronteiras de autoridade para o espelho `superpowers` no GitHub Copilot, evitando duplicação de regra e drift sem dono.

## Camadas oficiais

### Camada 1 — Semantic source of truth

`/.claude/`

Esta é a fonte de verdade semântica do sistema original `superpowers`.

Ela define:

- intenção do workflow;
- enforcement esperado;
- contratos de orquestração;
- shape do comportamento original.

Ela não define automaticamente:

- como o Copilot implementará a adaptação;
- quais artifacts do VS Code serão usados;
- como o mirror global será instalado.

### Camada 2 — Functional reference mirror

`/.windsurf/`

Esta camada é referência funcional útil para empacotamento e encadeamento do workflow, mas não é a autoridade primária quando houver divergência semântica com `/.claude/`.

Uso permitido:

- naming e packaging de skills;
- ordem prática do workflow;
- shape de entregáveis por etapa.

Uso proibido:

- sobrepor uma regra de enforcement do Claude;
- redefinir gate sem alinhar com a baseline canônica.

### Camada 3 — Copilot adaptation source of truth

`/.githubcopilot/`

Esta é a fonte de verdade da adaptação para GitHub Copilot.

Ela define:

- quais artifacts do Copilot implementam cada capability;
- como os gaps de runtime são tratados;
- como o usuário descobre e invoca o espelho no VS Code;
- quais claims são nativos e quais são apenas guiados.

Ela não pode:

- reescrever a baseline canônica silenciosamente;
- inventar paridade inexistente;
- competir com `/.claude/` como fonte da intenção original.

### Camada 4 — Global runtime mirror

`~/.githubcopilot-global/`

Esta camada existe para instalação e reutilização cross-project.

Ela é derivada da camada 3.

Ela não é fonte de verdade e não deve receber edição manual como origem primária.

## Tabela de autoridade

| Tema | Autoridade primária | Autoridade secundária | Não-autoridade |
| --- | --- | --- | --- |
| intenção do workflow `superpowers` | `/.claude/` | `/.windsurf/` | `~/.githubcopilot-global/` |
| ordem da cadeia canônica | `/.claude/` | `/.windsurf/` | `~/.githubcopilot-global/` |
| packaging prático das skills | `/.githubcopilot/` | `/.windsurf/` | `~/.githubcopilot-global/` |
| claims de compatibilidade Copilot | `/.githubcopilot/` | none | `/.windsurf/`, `~/.githubcopilot-global/` |
| discovery no VS Code | `/.githubcopilot/` | settings locais do usuário | `/.claude/` |
| instalação global | `/.githubcopilot/` | `~/.githubcopilot-global/` | `/.claude/` |
| drift table | `/.githubcopilot/` | `~/.githubcopilot-global/` | `/.claude/` |

## Regras de promoção

### Workspace -> global

Só pode promover para `~/.githubcopilot-global/` quando:

- o artifact já existe e está estável em `/.githubcopilot/`;
- o comportamento foi revisado no workspace;
- a classificação de paridade foi registrada;
- o impacto em discovery foi verificado.

### Global -> workspace

Não usar o mirror global como origem de desenvolvimento. Se houver melhoria descoberta no ambiente global, ela deve voltar primeiro para `/.githubcopilot/` e só depois ser promovida novamente.

## Regras de drift

Drift aceitável:

- arquivos estritamente runtime-specific no mirror global;
- settings locais do usuário que não alteram a semântica dos workflows.

Drift não aceitável:

- mudança de conteúdo de prompt, skill, instruction ou agent apenas no mirror global;
- alteração do fluxo `superpowers` fora da camada `/.githubcopilot/`;
- paridade declarada no runtime sem atualização da matriz de compatibilidade.

## Regras de edição por tipo de mudança

### Mudança de intenção do workflow original

Editar primeiro `/.claude/` e depois revalidar baseline e compatibilidade.

### Mudança de adaptação para Copilot

Editar primeiro `/.githubcopilot/`.

### Mudança de instalação/discovery global

Editar primeiro `/.githubcopilot/`, validar, e só depois sincronizar para `~/.githubcopilot-global/`.

## Política de claims

Toda claim relevante deve nascer na camada correta.

### Claims permitidas em `/.claude/`

- objetivo do workflow;
- enforcement esperado;
- gates semânticos.

### Claims permitidas em `/.githubcopilot/`

- como a adaptação foi implementada;
- qual artifact sustenta cada capability;
- qual gap residual continua aberto.

### Claims proibidas em `~/.githubcopilot-global/`

- redefinir semântica;
- redefinir autoridade;
- criar nova regra local sem upstream.

## Processo mínimo para evitar duplicação

1. verificar se a mudança é semântica, adaptativa ou operacional;
2. editar apenas a camada de autoridade primária;
3. registrar impacto na matriz de compatibilidade quando houver mudança de capability;
4. promover para o mirror global apenas depois da validação.

## Rollback model

### Rollback de adaptação Copilot

Reverter a mudança em `/.githubcopilot/` e sincronizar novamente o mirror global.

### Rollback de mirror global

Reinstalar a última versão aprovada derivada de `/.githubcopilot/`.

### Rollback de baseline

Se a baseline canônica mudar indevidamente, corrigir em `/.claude/` ou no documento de baseline antes de qualquer nova promoção.

## Consequência para os próximos batches

Os próximos batches devem assumir:

- `.claude/` como origem semântica;
- `.githubcopilot/` como origem da adaptação;
- `~/.githubcopilot-global/` como espelho derivado;
- `.windsurf/` como referência funcional auxiliar, nunca como autoridade final quando houver conflito.