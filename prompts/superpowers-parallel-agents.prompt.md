---
agent: agent
description: "Dispatch multiple independent tasks or investigations in parallel using subagents"
---

# /superpowers-parallel-agents

Execute tarefas independentes em paralelo via subagentes.

## Quando usar

- 2+ falhas de teste em arquivos/subsistemas diferentes e independentes;
- 2+ tarefas do plano sem dependência entre si;
- investigações múltiplas que não compartilham estado.

**Não usar quando:** falhas são relacionadas (corrigir uma pode corrigir outras) ou agentes precisariam editar os mesmos arquivos.

## O que fazer

1. **Identificar domínios independentes:** agrupar falhas/tarefas por subsistema ou arquivo de origem.
2. **Criar prompts focados** para cada agente com:
   - escopo específico (um arquivo/subsistema);
   - objetivo claro ("make these tests pass" / "implement task N");
   - restrições ("do NOT change production code" / "fix tests only");
   - output esperado ("summary of root cause and changes").
3. **Dispatch em paralelo:** chamar `task` tool múltiplas vezes na mesma resposta (modo background).
4. **Aguardar todos os agentes** completarem.
5. **Revisar e integrar:**
   - ler cada summary;
   - verificar que os fixes não conflitam;
   - rodar a suíte completa de testes;
   - integrar todas as mudanças.

## Regras

- cada agente deve ter contexto **completo e isolado** — nunca herdar o histórico da sessão atual;
- ser específico no prompt: escopo estreito + output definido = resultado previsível;
- não usar agentes paralelos por ornamentação — só quando as tarefas são genuinamente independentes;
- após integração, invocar `/superpowers-verification-before-completion`.

## Output esperado

```markdown
## Parallel Agents

**Independent domains identified:** N
**Agents dispatched:** N
**Results summary:**
  - Domain A: [o que foi encontrado/corrigido]
  - Domain B: [o que foi encontrado/corrigido]
**Conflicts:** [nenhum | descrição]
**Full test suite:** <pass|fail>
**Next step:** /superpowers-verification-before-completion
```
