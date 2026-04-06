---
title: Superpowers plugin bridge
area: superpowers-plugin-bridge
tags:
  - superpowers
  - plugin
  - opencode
  - bridge
  - copilot
globs:
  - "**"
updated: 2026-04-06
---

## Objetivo

Documentar como cada elemento do plugin `superpowers.js` (OpenCode, v5.0.7) é reproduzido no ambiente GitHub Copilot e registrar os gaps residuais de fidelidade.

## Fontes analisadas

- Oficial: `https://github.com/obra/superpowers` (repo canônico, autor Jesse Vincent / Prime Radiant)
- Cache local: `~/.claude/plugins/cache/claude-plugins-official/superpowers/5.0.7/.opencode/plugins/superpowers.js`
- `skills/using-superpowers/SKILL.md` (verificado contra HEAD do repo oficial)
- `skills/using-superpowers/references/copilot-tools.md` (verificado contra HEAD do repo oficial)

## Mapeamento de elementos do plugin

### Elemento 1 — Config hook (`config.skills.paths`)

**O que faz no OpenCode:**
Adiciona `../../skills` ao array `config.skills.paths` do OpenCode em tempo de carregamento, sem necessitar symlinks ou edição manual de arquivos de configuração. O singleton `Config.get()` é modificado e os skills são descobertos de forma lazy depois.

**Equivalente Copilot:**
| Mecanismo OpenCode | Equivalente Copilot | Paridade |
|---|---|---|
| `config.skills.paths` automático | `chat.agentSkillsLocations` no VS Code settings | guided parity |

**Implementação:**
```json
{
  "chat.agentSkillsLocations": ["C:/Users/<user>/.githubcopilot-global/skills"]
}
```

**Gap residual:**
O VS Code não recarrega a config dinamicamente como o singleton do OpenCode. A configuração precisa estar presente antes da sessão iniciar.

---

### Elemento 2 — Bootstrap injection (`experimental.chat.messages.transform` / `experimental.chat.system.transform`)

**O que faz no OpenCode (v5.0.7):**
Intercepta o array de mensagens e insere o conteúdo completo do `using-superpowers/SKILL.md` (sem frontmatter) + tool mapping como o primeiro `part` da primeira mensagem do usuário. O guard `EXTREMELY_IMPORTANT` impede injeção duplicada. A injeção só acontece uma vez por sessão.

**O que fazia no OpenCode (v5.0.6):**
Usava `experimental.chat.system.transform` para adicionar o conteúdo ao array `output.system`. Substituído na v5.0.7 por message transform para evitar token bloat em system messages e incompatibilidades com modelos como Qwen (#750, #894).

**Equivalente Copilot:**
| Mecanismo OpenCode | Equivalente Copilot | Paridade |
|---|---|---|
| `experimental.chat.messages.transform` | `instructions/*.instructions.md` com `applyTo: "**"` | guided parity |

**Implementação:**
`instructions/35-superpowers-bootstrap.instructions.md`

Esse arquivo contém:
- A regra `<EXTREMELY_IMPORTANT>` da 1% rule
- O workflow de invocação de prompts (check before any response)
- A priority ordering (process prompts first)
- A instruction hierarchy (user > superpowers > default)
- O tool mapping Claude Code → Copilot
- O SUBAGENT-STOP boundary
- O config bridge snippet (guia de settings)

**Gaps residuais:**
- O OpenCode injeta dinamicamente apenas na **primeira** mensagem de cada sessão; o Copilot inclui as instructions em **toda** mensagem (token overhead maior).
- O Copilot não fornece guard de deduplicação como o `if (firstUser.parts.some(p => p.text.includes('EXTREMELY_IMPORTANT'))) return`.
- O Copilot não executa código no momento da injeção; o conteúdo é estático (não lê `SKILL.md` ao vivo).

---

### Elemento 3 — Skills directory registration (NATIVE PARITY ACHIEVED)

**O que faz no OpenCode:**
O plugin registra `../../skills` relativamente a `__dirname` (`/.opencode/plugins/`), o que aponta para a pasta `skills/` no root do plugin. A discovery subsequente é feita pelo mecanismo lazy do OpenCode.

**Equivalente Copilot — Layer 1 (Guided Parity — Prompts):**
Os skills canônicos (`brainstorming`, `writing-plans`, `executing-plans`, etc.) estão disponíveis como **prompts** (`.prompt.md`) — entry points manuais equivalentes. A disciplina de cada skill está embutida no texto do prompt correspondente.

**Equivalente Copilot — Layer 2 (Native Parity — Skills Directory) ✅ ATIVO:**
Todos os 14 SKILL.md files foram copiados verbatim do HEAD do SSOT (`obra/superpowers`) para `~/.githubcopilot-global/skills/`. Descobertos automaticamente via `chat.agentSkillsLocations` no VS Code settings. O `skill` tool funciona identicamente ao `Skill` tool do Claude Code.

| Skill canônico OpenCode | Prompts (guided) | Skills/ (native) | Paridade |
|---|---|---|---|
| `skills/using-superpowers/SKILL.md` | `instructions/35-superpowers-bootstrap.instructions.md` | `skills/using-superpowers/SKILL.md` ✅ | **native** |
| `skills/brainstorming/SKILL.md` | `prompts/superpowers-brainstorming.prompt.md` | `skills/brainstorming/SKILL.md` ✅ | **native** |
| `skills/writing-plans/SKILL.md` | `prompts/superpowers-writing-plans.prompt.md` | `skills/writing-plans/SKILL.md` ✅ | **native** |
| `skills/using-git-worktrees/SKILL.md` | `prompts/superpowers-git-worktrees.prompt.md` | `skills/using-git-worktrees/SKILL.md` ✅ | **native** |
| `skills/executing-plans/SKILL.md` | `prompts/superpowers-executing-plans.prompt.md` | `skills/executing-plans/SKILL.md` ✅ | **native** |
| `skills/subagent-driven-development/SKILL.md` | `prompts/superpowers-subagent-driven-development.prompt.md` | `skills/subagent-driven-development/SKILL.md` ✅ | **native** |
| `skills/dispatching-parallel-agents/SKILL.md` | `prompts/superpowers-parallel-agents.prompt.md` | `skills/dispatching-parallel-agents/SKILL.md` ✅ | **native** |
| `skills/verification-before-completion/SKILL.md` | `prompts/superpowers-verification-before-completion.prompt.md` | `skills/verification-before-completion/SKILL.md` ✅ | **native** |
| `skills/test-driven-development/SKILL.md` | `prompts/superpowers-tdd.prompt.md` | `skills/test-driven-development/SKILL.md` ✅ | **native** |
| `skills/systematic-debugging/SKILL.md` | `prompts/superpowers-debugging.prompt.md` | `skills/systematic-debugging/SKILL.md` ✅ | **native** |
| `skills/requesting-code-review/SKILL.md` | `prompts/superpowers-code-review.prompt.md` | `skills/requesting-code-review/SKILL.md` ✅ | **native** |
| `skills/receiving-code-review/SKILL.md` | `prompts/superpowers-receiving-review.prompt.md` | `skills/receiving-code-review/SKILL.md` ✅ | **native** |
| `skills/finishing-a-development-branch/SKILL.md` | `prompts/superpowers-finishing-branch.prompt.md` | `skills/finishing-a-development-branch/SKILL.md` ✅ | **native** |
| `skills/writing-skills/SKILL.md` | — | `skills/writing-skills/SKILL.md` ✅ | **native** |
| `skills/using-superpowers/references/copilot-tools.md` | Tool mapping em `instructions/35` | `skills/using-superpowers/references/copilot-tools.md` ✅ | **native** |
| `skills/using-superpowers/references/copilot-tools-windows.md` | — (Windows-only supplement) | `skills/using-superpowers/references/copilot-tools-windows.md` ✅ | **native+** |
| `skills/brainstorming/visual-companion.md` | — | `skills/brainstorming/visual-companion.md` ✅ | **native** |
| `skills/brainstorming/spec-document-reviewer-prompt.md` | — | `skills/brainstorming/spec-document-reviewer-prompt.md` ✅ | **native** |
| `skills/requesting-code-review/code-reviewer.md` | — | `skills/requesting-code-review/code-reviewer.md` ✅ | **native** |

**Nota:** `writing-skills` não possui prompt.md correspondente — apenas disponível via native skill layer.

**Gap residual:**
No OpenCode, o `Skill` tool carrega o conteúdo dinamicamente. No Copilot, o `skill` tool descobre os skills via `chat.agentSkillsLocations` — **gap fechado na camada nativa**.

---

### Elemento 4 — Tool mapping

**O que faz no OpenCode:**
O plugin gera uma string `toolMapping` que mapeia nomes de ferramentas do Claude Code para equivalentes OpenCode (`TodoWrite` → `todowrite`, `Task` → sistema de `@mention`, etc.) e a adiciona ao conteúdo injetado.

**Equivalente Copilot:**
A tabela de mapeamento está embutida em `instructions/35-superpowers-bootstrap.instructions.md` (seção "Tool Mapping for Superpowers Skills").

O mapeamento canônico oficial para Copilot CLI (`skills/using-superpowers/references/copilot-tools.md` no repo `obra/superpowers`) inclui adicionalmente:
- `store_memory` — persistir fatos do codebase entre sessões
- `report_intent` — atualizar status na UI
- `bash` async + `write_bash` / `read_bash` / `stop_bash` / `list_bash` — sessões de shell persistentes (no ambiente Windows do Copilot: `powershell` async + `write_powershell` / `read_powershell` / `stop_powershell`)
- `github-mcp-server-*` tools — acesso nativo à API do GitHub (issues, PRs, code search)

Todos esses foram incorporados ao arquivo de bootstrap.

**Paridade:** guided parity — o mapeamento existe, mas não é injetado dinamicamente; está em arquivo estático always-on.

---

### Elemento 5 — Frontmatter stripping

**O que faz no OpenCode:**
A função `extractAndStripFrontmatter` remove o bloco `---\n...\n---\n` do início do `SKILL.md` antes de injetar no contexto, para evitar expor metadados YAML desnecessários ao modelo.

**Equivalente Copilot:**
As instructions files no Copilot são consumidas pelo motor como contexto e o frontmatter YAML é reconhecido nativamente. Não há necessidade de strip manual — o Copilot processa o frontmatter como metadado e não o envia como conteúdo bruto para o modelo.

**Paridade:** native parity (diferente mecanismo, mesmo resultado).

---

### Elemento 6 — `normalizePath` e `OPENCODE_CONFIG_DIR`

**O que faz no OpenCode:**
Normaliza o path de configuração expandindo `~` e usando `OPENCODE_CONFIG_DIR` como override via variável de ambiente. Garante que o skills path seja absoluto e portátil.

**Equivalente Copilot:**
O VS Code settings usa paths absolutos (`C:/Users/<user>/...`) conforme documentado em `knowledge-base/19-superpowers-global-profile-activation.md`. Não há variável de ambiente equivalente, mas o settings de workspace pode sobrescrever o settings global.

**Paridade:** guided parity — portabilidade é manual (o usuário edita o settings).

---

## Residual Gap Register (atualizado 2026-04-06)

| Gap | Severidade | Status | Mitigação |
|---|---|---|---|
| Injeção single-shot (apenas primeira mensagem) vs always-on instructions | low | open | token overhead é pequeno; o conteúdo é conciso |
| Sem guard de deduplicação por sessão | low | open | idempotente por design; repetição não causa dano funcional |
| Skills não carregados dinamicamente pelo `Skill` tool nativo | medium | **FECHADO** | `chat.agentSkillsLocations` + `skills/` dir com 14 SKILL.md verbatim do SSOT |
| `chat.*FilesLocations` apontando para `C:/Users/win/` (caminho errado) | critical | **FECHADO** | ADV-001: todos os 4 keys fixados para `C:/Users/ferna/` em settings.json (2026-04-06) |
| `copilot-tools.md` SSOT referencia `bash` (Linux/Mac) | medium | **FECHADO** | ADV-003: adicionado `copilot-tools-windows.md` como override aditivo; `instructions/35` é autoridade primária |
| Config hook não é automático | low | open | documentado em KB-19; o usuário configura uma vez |
| `OPENCODE_CONFIG_DIR` override sem equivalente | low | open | sem casos de uso pendentes no contexto Copilot |
| `writing-skills` sem prompt.md correspondente | low | open | disponível via native skill layer; prompt.md não é necessário |

## Como verificar a bridge em runtime

1. Abrir VS Code com o settings de perfil do usuário apontando para `~/.githubcopilot-global/`.
2. Iniciar uma nova sessão no Copilot Chat.
3. Confirmar que `35-superpowers-bootstrap.instructions.md` está sendo aplicado (checar com `/superpowers-start` se o agente identifica corretamente a etapa e menciona a 1% rule).
4. Verificar estrutura manualmente: confirmar que `~/.githubcopilot-global/skills/` contém os 14 SKILL.md e que `settings.json` tem `chat.agentSkillsLocations` apontando para o diretório correto.

## Batch boundary

Este KB fecha o bridge analysis do plugin.

O próximo incremento pode:
- Adicionar `35-superpowers-bootstrap.instructions.md` e este KB ao approved set em KB-17 para inclusão no sync;
- Validar comportamento real do bootstrap em sessão live;
- Considerar um smoke test para a 1% rule no harness.
