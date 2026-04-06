---
agent: agent
description: "Systematic debugging — find root cause before attempting fixes"
---

# /superpowers-debugging

Fixes aleatórios desperdiçam tempo e criam novos bugs. Patches rápidos mascaram problemas reais.

**Princípio central:** SEMPRE encontre a causa raiz antes de tentar corrigir. Fix de sintoma é falha.

**Violar a letra deste processo é violar o espírito do debugging.**

## The Iron Law

```
NENHUM FIX SEM INVESTIGAÇÃO DE CAUSA RAIZ PRIMEIRO
```

Se você não completou a Phase 1, não pode propor fixes.

## Quando usar

Use para QUALQUER issue técnico: falhas de teste, bugs em produção, comportamento inesperado, problemas de performance, falhas de build, issues de integração.

**Use ESPECIALMENTE quando:**
- Sob pressão de tempo (emergências tornam adivinhação tentadora)
- "Só um fix rápido" parece óbvio
- Já tentou múltiplos fixes
- Fix anterior não funcionou
- Não entende completamente o issue

**Não pule quando:**
- Issue parece simples (bugs simples têm causa raiz também)
- Está com pressa (sistemático é mais rápido que thrashing)
- Querem resolvido AGORA (sistemático é mais rápido que guess-and-check)

## O que fazer

Você DEVE completar cada fase antes de prosseguir para a próxima.

### Phase 1 — Root Cause Investigation

**ANTES de tentar QUALQUER fix:**

1. **Ler mensagens de erro com cuidado** — não pular erros ou warnings. Ler stack traces completamente. Anotar line numbers, file paths, error codes.
2. **Reproduzir consistentemente** — consegue disparar de forma confiável? Quais os passos exatos? Acontece toda vez? Se não reproduzível → coletar mais dados, não adivinhar.
3. **Verificar mudanças recentes** — o que mudou que pode causar isso? Git diff, commits recentes, novas dependências, mudanças de config, diferenças de ambiente.
4. **Coletar evidência em sistemas multi-componente** — para cada fronteira de componente: logar o que entra, logar o que sai, verificar propagação de env/config. Rodar uma vez para coletar evidência mostrando ONDE quebra.
   - Para CADA fronteira de componente:
     - logar o que entra;
     - logar o que sai;
     - verificar propagação de environment/config;
     - checar estado em cada camada.
   - Exemplo de instrumentação:
```bash
# Layer 1: Workflow
echo "=== Secrets available in workflow: ==="
echo "IDENTITY: ${IDENTITY:+SET}${IDENTITY:-UNSET}"

# Layer 2: Build script
echo "=== Env vars in build script: ==="
env | grep IDENTITY || echo "IDENTITY not in environment"

# Layer 3: Signing script
echo "=== Keychain state: ==="
security list-keychains
security find-identity -v

# Layer 4: Actual signing
codesign --sign "$IDENTITY" --verbose=4 "$APP"
```
   - Isso revela qual camada falha (ex.: secrets → workflow ✓, workflow → build ✗).
5. **Rastrear fluxo de dados** — onde o valor errado se origina? O que chamou com valor errado? Continuar rastreando até encontrar a fonte. Corrigir na fonte, não no sintoma.
   - Ver também a técnica completa em `root-cause-tracing.md`.

### Phase 2 — Pattern Analysis

1. **Encontrar exemplos funcionais** — localizar código similar que funciona no mesmo codebase.
2. **Comparar contra referências** — se implementando um padrão, ler a implementação de referência COMPLETAMENTE. Não skimmar.
3. **Identificar diferenças** — o que é diferente entre o que funciona e o que está quebrado? Listar toda diferença, por menor que seja.
4. **Entender dependências** — que outros componentes são necessários? Que settings, config, ambiente?

### Phase 3 — Hypothesis and Testing

1. **Formar UMA hipótese** — declarar claramente: "Acho que X é a causa raiz porque Y". Escrever. Ser específico.
2. **Testar minimamente** — fazer a MENOR mudança possível para testar a hipótese. Uma variável por vez. Não corrigir múltiplas coisas de uma vez.
3. **Verificar antes de continuar** — funcionou? Sim → Phase 4. Não → formar NOVA hipótese. NÃO empilhar mais fixes.
4. **Quando não sabe** — dizer "Não entendo X". Não fingir que sabe. Pedir ajuda.

### Phase 4 — Implementation

1. **Criar teste falhando** — reprodução mais simples possível. Teste automatizado se possível. Script one-off se não houver framework. DEVE existir antes de corrigir. Usar `/superpowers-tdd` para escrever testes adequados.
2. **Implementar fix único** — endereçar a causa raiz identificada. UMA mudança por vez. Sem "já que estou aqui".
3. **Verificar fix** — teste passa? Outros testes não quebraram? Issue realmente resolvido?
4. **Se fix não funcionar** — PARAR. Contar: quantos fixes já tentou?
   - Se < 3: voltar à Phase 1, re-analisar com nova informação
   - Se ≥ 3: PARAR e questionar a arquitetura
   - NÃO tentar Fix #4 sem discussão arquitetural.
### Phase 5 — Architectural Review

Se 3+ fixes falharam: **Questionar Arquitetura**. Cada fix revela novo problema em lugar diferente? Fixes exigem refactoring massivo? Cada fix cria novos sintomas?

Então pare e discuta com o usuário antes de tentar mais fixes.

## Regras

- não adivinhar causa raiz;
- não mascarar sintomas;
- não corrigir antes de entender o problema;
- documentar cada fase com output visível;
- uma variável por vez ao testar hipóteses;
- escalar após 3 fixes falhos — questionar arquitetura;
- quando a correção exigir proteção de regressão, encaminhar para `/superpowers-tdd`;
- encerrar com handoff para `/superpowers-verification-before-completion`.

## Sinais do usuário de que você está errando

Se o usuário sinalizar coisas como:
- "Isso não está acontecendo?"
- "Isso vai nos mostrar onde quebra?"
- "Pare de adivinhar"
- "Estamos travados?"

Pare e volte para a Phase 1 com coleta de evidência.

## Red Flags — PARE e Volte à Phase 1

Se você se pegar pensando:
- "Fix rápido por agora, investigar depois"
- "Só tentar mudar X e ver se funciona"
- "Adicionar múltiplas mudanças, rodar testes"
- "Pular o teste, vou verificar manualmente"
- "Provavelmente é X, deixa eu corrigir"
- "Não entendo completamente mas pode funcionar"
- "O padrão diz X mas vou adaptar diferente"
- "Aqui estão os problemas principais: [lista fixes sem investigação]"
- propondo soluções antes de rastrear fluxo de dados
- "mais uma tentativa" quando já tentou 2+

Todos esses sinais significam: PARAR e voltar para a Phase 1.

## Anti-patterns

| Anti-pattern | Faça em vez disso |
|---|---|
| "Fix rápido agora, investigar depois" | Investigue causa raiz antes de qualquer fix |
| Múltiplas mudanças de uma vez | Uma variável por vez |
| Corrigir na camada errada | Rastrear até a origem do problema |
| Retry infinito | Após 3 falhas, questionar arquitetura |
| Sem validação após fix | Sempre validar com teste + checks |

## Common Rationalizations

| Desculpa | Realidade |
|----------|----------|
| "Issue é simples, não precisa de processo" | Issues simples têm causa raiz também. |
| "Emergência, sem tempo" | Sistemático é mais rápido que guess-and-check. |
| "Só tentar isso primeiro" | Primeiro fix define o padrão. Faça certo desde o início. |
| "Vou escrever teste depois" | Fix sem teste não dura. |
| "Múltiplos fixes economizam tempo" | Não isola a causa e gera bugs novos. |
| "Referência é longa, vou adaptar" | Entendimento parcial garante bugs. |
| "Eu vejo o problema, vou corrigir" | Ver sintomas ≠ entender causa raiz. |
| "Só mais uma tentativa" | 3+ falhas indicam problema arquitetural. |

## Quando o processo não revela causa raiz

Se investigação completa mostrar issue ambiental, de timing, ou externo:
1. Documente o que investigou
2. Implemente tratamento adequado (retry/timeout/mensagem)
3. Adicione monitoramento/logging

Mas lembre: na maioria dos casos, "sem causa raiz" significa investigação incompleta.

**Regra prática:** em ~95% dos casos, "sem causa raiz" é investigação incompleta.

## Supporting Techniques

- `root-cause-tracing.md` — rastreio reverso para encontrar o gatilho original;
- `defense-in-depth.md` — validações em múltiplas camadas após achar a causa raiz;
- `condition-based-waiting.md` — substituir timeouts arbitrários por condition polling.

**Related prompts:**
- `/superpowers-tdd` — criar teste falhando e proteger regressão;
- `/superpowers-verification-before-completion` — validar fix antes de claim final.

## Real-World Impact

- abordagem sistemática: 15–30 min para fix;
- tentativa aleatória: 2–3 horas de thrashing;
- first-time fix rate: ~95% vs ~40%;
- bugs novos introduzidos: próximo de zero vs frequente.

## Quick Reference

| Phase | Atividades-chave | Critério de sucesso |
|-------|-------------------|---------------------|
| Phase 1 | Ler erros, reproduzir, verificar mudanças, coletar evidência | Entender O QUÊ e POR QUÊ |
| Phase 2 | Encontrar exemplos, comparar | Identificar diferenças |
| Phase 3 | Formar teoria, testar minimamente | Confirmada ou nova hipótese |
| Phase 4 | Criar teste, corrigir, verificar | Bug resolvido, testes passam |

## Output esperado

```markdown
## Superpowers Debugging

### Bug Report
- **Esperado:** [comportamento esperado]
- **Observado:** [comportamento real]
- **Reprodutível:** [sempre|intermitente|condicional]

### Phase 1 — Root Cause
- **Mudanças recentes:** [o que mudou]
- **Evidência coletada:** [onde quebra]
- **Fluxo de dados:** [origem do valor errado]

### Phase 2 — Pattern Analysis
- **Exemplo funcional:** [código similar que funciona]
- **Diferenças:** [o que é diferente]

### Phase 3 — Hypothesis
| # | Hipótese | Veredicto | Evidência |
|---|----------|-----------|-----------|
| 1 | [hipótese] | CONFIRMADA/NEGADA | [evidência] |

### Phase 4 — Implementation
- **Causa raiz:** [tipo] em `file.py:line` — [explicação]
- **Fix:** [mudança mínima aplicada]
- **Teste:** [teste falhando criado e agora passa]

### Next prompt
/superpowers-tdd ou /superpowers-verification-before-completion
```