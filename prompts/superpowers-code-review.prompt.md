---
agent: agent
description: "Request structured code review with dedicated reviewer dispatch"
---

# /superpowers-code-review

Solicite uma revisão estruturada e encaminhe para o reviewer dedicado.

**Princípio central:** revise cedo, revise frequentemente.

## Modelo de operação

Este prompt é **requester-only**: prepara contexto, delimita o escopo e despacha para `/superpowers-code-reviewer`.

O reviewer dedicado é responsável por avaliar qualidade, arquitetura, performance, testes, aderência a requisitos e prontidão de produção.
Inclua no pacote de review os riscos de **corretude** e **segurança** para análise explícita.

## Quando solicitar review

**Obrigatório:**
- Após cada task em subagent-driven development
- Após completar feature principal
- Antes de merge para main

**Opcional mas valioso:**
- Quando travado (perspectiva fresca)
- Antes de refatoração (baseline check)
- Após corrigir bug complexo

## O que fazer (requester)

### Step 1 — Preparar contexto

1. Registrar contexto mínimo do request:
   - `{WHAT_WAS_IMPLEMENTED}`
   - `{PLAN_OR_REQUIREMENTS}`
   - `{BASE_SHA}`
   - `{HEAD_SHA}`
   - `{DESCRIPTION}`
2. Obter SHAs quando não vierem prontos:
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # ou origin/main
HEAD_SHA=$(git rev-parse HEAD)
```
3. Obter e revisar o diff da faixa correta:
   - `git diff --stat {BASE_SHA}..{HEAD_SHA}`
   - `git diff {BASE_SHA}..{HEAD_SHA}`
4. Consolidar descrição objetiva do que foi implementado e critérios esperados.

### Step 2 — Despachar para reviewer dedicado

Encaminhar o pacote de contexto para `/superpowers-code-reviewer` com:
- `{WHAT_WAS_IMPLEMENTED}`
- `{PLAN_OR_REQUIREMENTS}`
- `{BASE_SHA}`
- `{HEAD_SHA}`
- `{DESCRIPTION}`

### Step 3 — Receber veredito e decidir ação

- Se **APPROVE**: seguir para verificação final.
- Se **REQUEST CHANGES**: corrigir Important/Critical e pedir nova revisão.
- Se **BLOCK**: parar avanço, resolver blockers e reexecutar review.

### Step 4 — Registrar decisão

- Registrar resumo da revisão, principais issues e decisão operacional.
- Encerrar com handoff para `/superpowers-verification-before-completion`.

## Regras

- não executar review técnico completo neste prompt;
- sempre despachar para `/superpowers-code-reviewer`;
- não prosseguir com Critical/Important pendentes;
- manter rastreabilidade por SHA e escopo.

**DO:**
- fornecer contexto completo e preciso;
- garantir faixa de diff correta (`BASE_SHA..HEAD_SHA`);
- aplicar decisão do reviewer com disciplina.

**DON'T:**
- revisar por conta própria e pular reviewer dedicado;
- despachar sem SHAs/contexto mínimos;
- ignorar BLOCK.

## Anti-patterns

| Anti-pattern | Faça em vez disso |
|---|---|
| Pular review porque "é simples" | Revisar sempre nos gatilhos obrigatórios |
| Ignorar Critical | Corrigir imediatamente |
| Prosseguir com Important pendente | Resolver antes de avançar |
| Feedback vago sem evidência | Sempre referenciar `file:line` e justificativa |

## Exemplo rápido de fluxo

1. Capturar `BASE_SHA` e `HEAD_SHA`.
2. Preparar pacote de contexto.
3. Despachar para `/superpowers-code-reviewer`.
4. Aplicar decisão (APPROVE / REQUEST CHANGES / BLOCK).
5. Handoff para `/superpowers-verification-before-completion`.

## Output esperado

```markdown
## Superpowers Code Review

### Request Package
- WHAT_WAS_IMPLEMENTED: [resumo objetivo]
- PLAN_OR_REQUIREMENTS: [fonte]
- BASE_SHA: [sha]
- HEAD_SHA: [sha]
- DESCRIPTION: [escopo e riscos]

### Dispatch
Forwarding to: /superpowers-code-reviewer

### Reviewer Verdict
[APPROVE|REQUEST CHANGES|BLOCK]

### Action Taken
- [correções aplicadas / blockers resolvidos / re-review solicitado]

### Assessment
**Ready to proceed?** [Yes|No|With fixes]
**Reasoning:** [avaliação técnica em 1-2 frases]

### Next prompt
/superpowers-verification-before-completion
```