---
agent: agent
description: "Complete a development branch — verify tests, then merge, PR, keep or discard"
---

# /superpowers-finishing-branch

Execute a etapa de conclusão de branch do workflow `superpowers`.

## O que fazer

1. **Anunciar:** "I'm using the finishing-a-development-branch skill to complete this work."
2. **Verificar testes:** rodar a suíte completa — se falhar, parar e reportar as falhas. Não prosseguir.
3. **Determinar base branch:** `git merge-base HEAD main` (ou `master`) — ou perguntar se ambíguo.
4. **Apresentar exatamente 4 opções:**
   ```
   Implementation complete. What would you like to do?

   1. Merge back to <base-branch> locally
   2. Push and create a Pull Request
   3. Keep the branch as-is (I'll handle it later)
   4. Discard this work

   Which option?
   ```
5. **Executar a escolha:**
   - **Opção 1 (merge local):** checkout base → pull → merge feature → verificar testes → deletar branch → limpar worktree.
   - **Opção 2 (PR):** `git push -u origin <branch>` → criar PR com summary + test plan via `gh pr create` — manter worktree.
   - **Opção 3 (manter):** apenas reportar branch e caminho do worktree. Não limpar nada.
   - **Opção 4 (descartar):** pedir confirmação digitada `discard` → checkout base → `git branch -D <branch>` → limpar worktree.
6. **Limpar worktree** apenas para opções 1 e 4: `git worktree remove <path>`.

## Regras

- **nunca** prosseguir com testes falhando;
- **nunca** fazer merge sem re-verificar testes no resultado final;
- **nunca** descartar sem confirmação digitada `discard`;
- apresentar exatamente 4 opções — sem variações;
- não limpar worktree para opções 2 e 3.

## Output esperado

```markdown
## Branch Completion

**Tests:** <pass|fail>
**Base branch:** <branch>
**Choice:** <1|2|3|4>
**Action taken:** [o que foi feito]
**Worktree:** <cleaned|preserved>
```
