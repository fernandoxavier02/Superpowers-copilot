---
agent: agent
description: "Process incoming code review feedback with technical rigor before implementing"
---

# /superpowers-receiving-review

Processa feedback de code review com rigor técnico antes de implementar.

## Padrão de resposta

Para cada item de feedback, executar na ordem:

1. **READ** — ler o feedback completo sem reagir.
2. **UNDERSTAND** — reformular o requisito com próprias palavras; se ambíguo, perguntar antes de qualquer implementação.
3. **VERIFY** — checar contra a realidade do codebase.
4. **EVALUATE** — é tecnicamente correto para *este* codebase? Viola YAGNI? Conflita com decisões arquiteturais anteriores?
5. **RESPOND** — acknowledgment técnico **ou** pushback fundamentado.
6. **IMPLEMENT** — um item de cada vez, testando cada um.

## Respostas proibidas

**NUNCA:**
- "You're absolutely right!" / "Great point!" / "Excellent feedback!" (performativo)
- "Let me implement that now" (antes de verificar)
- Qualquer expressão de gratidão

**Em vez disso:**
- reformular o requisito técnico;
- fazer perguntas de clarificação;
- pushback com raciocínio técnico se necessário;
- simplesmente começar a trabalhar (ações > palavras).

## Quando fazer pushback

- a sugestão quebra funcionalidade existente;
- o reviewer não tem contexto completo;
- viola YAGNI (feature não usada — verificar com `grep`);
- tecnicamente incorreto para este stack/platform;
- conflita com decisões arquiteturais do usuário.

**Como fazer pushback:** raciocínio técnico, não defensividade. Referenciar testes/código funcionando.

## Ordem de implementação (multi-item)

1. Clarificar itens ambíguos **primeiro** — todos antes de implementar qualquer coisa.
2. Blocking issues (crashes, security).
3. Simple fixes (typos, imports).
4. Complex fixes (refactoring, logic).
5. Testar cada fix individualmente + verificar regressões.

## Output esperado

```markdown
## Code Review Reception

**Items received:** N
**Items unclear (clarified first):** [lista ou "none"]
**Items implemented:** [lista com descrição sucinta]
**Items pushed back:** [lista com raciocínio]
**Tests after changes:** <pass|fail>
```
