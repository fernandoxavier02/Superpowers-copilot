---
agent: agent
description: "Set up an isolated git worktree for feature work before executing plans"
---

# /superpowers-git-worktrees

Execute a etapa de criação de worktree do workflow `superpowers`.

## O que fazer

1. **Anunciar:** "I'm using the using-git-worktrees skill to set up an isolated workspace."
2. **Selecionar diretório** na ordem de prioridade:
   - `.worktrees/` existe? → usar (verificar se está no .gitignore)
   - `worktrees/` existe? → usar (verificar se está no .gitignore)
   - CLAUDE.md / AGENTS.md indica preferência? → seguir
   - Nenhum dos dois? → perguntar ao usuário (`.worktrees/` local vs `~/.config/superpowers/worktrees/` global)
3. **Verificar segurança** (para dirs locais): `git check-ignore -q .worktrees` — se não ignorado, adicionar ao `.gitignore` e commitar antes de prosseguir.
4. **Criar worktree:** `git worktree add <path> -b <branch-name>`
5. **Setup do projeto** (auto-detectar): `npm install` / `cargo build` / `pip install -r requirements.txt` / `go mod download`
6. **Verificar baseline:** rodar a suíte de testes — se falhar, reportar e perguntar se prossegue.
7. **Reportar:** caminho completo do worktree + status dos testes.
8. **Handoff:** invocar `/superpowers-subagent-driven-development` ou `/superpowers-executing-plans`.

## Regras

- **nunca** criar worktree local sem verificar que o diretório está no `.gitignore`;
- se os testes falharem no baseline, **não prosseguir silenciosamente** — perguntar;
- não assumir o diretório sem checar a ordem de prioridade;
- no Windows, usar `powershell` equivalentes: `git check-ignore`, `git worktree add`, etc.

## Output esperado

```markdown
## Git Worktree Setup

**Worktree:** <full-path>
**Branch:** <branch-name>
**Baseline tests:** <pass|fail — N failures>
**Next step:** /superpowers-subagent-driven-development
```
