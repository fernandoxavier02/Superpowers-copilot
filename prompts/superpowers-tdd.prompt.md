---
agent: agent
description: "Run the superpowers TDD discipline — write test first, watch it fail, write minimal code to pass"
---

# /superpowers-tdd

Escreva o teste primeiro. Veja falhar. Escreva o mínimo para passar.

**Princípio central:** Se você não viu o teste falhar, não sabe se ele testa a coisa certa.

**Violar a letra das regras é violar o espírito das regras.**

## The Iron Law

```
NENHUM CÓDIGO DE PRODUÇÃO SEM UM TESTE FALHANDO PRIMEIRO
```

Escreveu código antes do teste? **Delete.** Comece de novo.

- Não guarde como "referência"
- Não "adapte" enquanto escreve testes
- Não olhe para ele
- Delete significa delete

Implemente do zero a partir dos testes. Ponto.

## Quando usar

**Sempre:** features novas, bug fixes, refatoração, mudanças de comportamento.

**Exceções (pergunte ao usuário):** protótipos descartáveis, código gerado, arquivos de configuração.

Pensando "pular TDD só dessa vez"? Pare. Isso é racionalização.

## O que fazer

### Phase 1 — RED (escrever teste que falha)

1. Escrever UM teste mínimo mostrando o que deveria acontecer.
2. Um comportamento por teste, nome descritivo, código real (mocks só se inevitável).
3. Rodar o teste e **confirmar que falha**.

**Teste bom:**
```python
def test_rejects_empty_email(self):
	"""GIVEN empty email WHEN submit THEN error returned."""
	result = submit_form({"email": ""})
	self.assertEqual(result["error"], "Email obrigatório")
```
Nome claro, testa comportamento real, uma coisa.

**Teste ruim:**
```python
def test_email(self):
	mock = MagicMock()
	mock.return_value = {"error": "Email obrigatório"}
	# Testa o mock, não o código
```
Nome vago, testa mock em vez de código.

### Verify RED (OBRIGATÓRIO, nunca pular)
- Teste falha (não dá erro de sintaxe)
- Mensagem de falha é a esperada
- Falha porque a feature não existe (não por typo)
- Teste passa? Você está testando comportamento existente. Corrija o teste.

### Phase 2 — GREEN (código mínimo)

1. Escrever o código **mais simples** para o teste passar.
2. Sem otimização, sem extras, sem "já que estou aqui".
3. Valores hardcoded são aceitáveis. Não adicionar features além do teste.

**Implementação boa:**
```python
def submit_form(data):
	if not (data.get("email") or "").strip():
		return {"error": "Email obrigatório"}
	# ...
```
Mínimo para passar.

**Implementação ruim:**
```python
def submit_form(data, options=None, validator=None, on_error=None):
	# YAGNI — over-engineered
```

### Verify GREEN (OBRIGATÓRIO)
- Teste passa
- Outros testes continuam passando
- Output limpo (sem erros, sem warnings)
- Teste falha? Corrija o código, não o teste.

### Phase 3 — REFACTOR (limpar)

1. Só depois do GREEN: remover duplicação, melhorar nomes, extrair helpers.
2. Manter testes verdes. Não adicionar comportamento.
3. Rodar testes após **cada** mudança. Desfazer se quebrar.

### Phase 4 — PROMOTE (regressão)

1. Testes viram regressão permanente.
2. Rodar suíte completa:
   - `python -m compileall`
   - `python -m unittest discover`

### Repetir

Próximo teste falhando para a próxima feature. Encerrar com handoff para `/superpowers-verification-before-completion`.

## Por que a ordem importa

- **"Vou testar depois"** — Testes escritos depois passam imediatamente. Passar imediatamente não prova nada. Você testa o que construiu, não o que é necessário.
- **"Já testei manualmente"** — Teste manual é ad-hoc. Sem registro, não re-executa, esquece casos sob pressão.
- **"Deletar X horas é desperdício"** — Sunk cost fallacy. Manter código não verificado é dívida técnica.
- **"TDD é dogmático"** — TDD É pragmático: encontra bugs antes do commit, previne regressões, documenta comportamento, habilita refatoração.
- **"Testes depois atingem o mesmo objetivo"** — Não. Testes-depois = "o que isso faz?" Testes-primeiro = "o que isso deveria fazer?".

## Regras

- teste PRIMEIRO — nunca escrever código de produção antes do teste;
- um comportamento por teste;
- nomes descritivos (`test_should_return_error_when_input_empty`);
- não pular o ciclo RED;
- não declarar sucesso sem evidência de teste;
- quando a implementação terminar, passar por `/superpowers-verification-before-completion`.

## Anti-patterns

| Anti-pattern | Faça em vez disso |
|---|---|
| Código antes do teste | Escreva teste primeiro e confirme RED |
| Teste passa imediatamente | Garanta falha real antes do GREEN |
| Over-engineering no GREEN | Código mínimo para passar |
| Pular REFACTOR | Refatore mantendo testes verdes |

## Verification Checklist

Antes de declarar trabalho completo:

- [ ] Toda nova função/método tem teste
- [ ] Viu cada teste falhar antes de implementar
- [ ] Cada teste falhou pelo motivo esperado (feature ausente, não typo)
- [ ] Escreveu código mínimo para passar cada teste
- [ ] Todos os testes passam
- [ ] Output limpo (sem erros, sem warnings)
- [ ] Testes usam código real (mocks só se inevitável)
- [ ] Edge cases e erros cobertos

Não consegue marcar todos? Você pulou TDD. Comece de novo.

## Common Rationalizations

| Desculpa | Realidade |
|----------|----------|
| "Simples demais para testar" | Código simples quebra. Teste leva 30 segundos. |
| "Vou testar depois" | Testes que passam imediatamente não provam nada. |
| "Testes depois atingem o mesmo objetivo" | Testes-depois = "o que faz?" Testes-primeiro = "o que deveria fazer?" |
| "Já testei manualmente" | Ad-hoc ≠ sistemático. Sem registro, não re-executa. |
| "Teste manual é mais rápido" | Manual não prova edge cases. Vai re-testar a cada mudança. |
| "Deletar X horas é desperdício" | Sunk cost fallacy. Manter código não verificado é dívida técnica. |
| "Guardar como referência" | Você vai adaptar. Isso é testar depois. Delete significa delete. |
| "Preciso explorar primeiro" | OK. Jogue fora a exploração, comece com TDD. |
| "Teste difícil = design obscuro" | Ouça o teste. Difícil de testar = difícil de usar. |
| "TDD vai me atrasar" | TDD é mais rápido que debugging. Pragmático = test-first. |
| "Código existente não tem testes" | Você está melhorando. Adicione testes para o código existente. |

## Red Flags — PARE e Comece de Novo

Se você se pegar pensando:
- Código antes do teste
- Teste depois da implementação
- Teste passa imediatamente
- Não consegue explicar por que o teste falhou
- Testes adicionados "depois"
- Racionalizando "só dessa vez"
- "Já testei manualmente"
- "Testes depois atingem o mesmo objetivo"
- "É sobre o espírito, não o ritual"
- "Guardar como referência"
- "Já gastei X horas, deletar é desperdício"
- "TDD é dogmático, estou sendo pragmático"
- "Isso é diferente porque..."

**Todos significam: Delete o código. Comece de novo com TDD.**

## When Stuck

| Problema | Solução |
|----------|---------|
| Não sabe como testar | Escreva a API desejada. Escreva a assertion primeiro. Pergunte ao usuário. |
| Teste muito complicado | Design muito complicado. Simplifique a interface. |
| Precisa mockar tudo | Código muito acoplado. Use dependency injection. |
| Setup do teste enorme | Extraia helpers. Ainda complexo? Simplifique o design. |

## Debugging Integration

Bug encontrado? Escreva teste falhando que reproduz. Siga o ciclo TDD. O teste prova o fix e previne regressão.

Nunca corrija bugs sem um teste.

## Final Rule

```
Código de produção → teste existe e falhou primeiro
Caso contrário → não é TDD
```

Sem exceções sem permissão explícita do usuário.

## Testing Anti-Patterns

Ao adicionar mocks ou utilitários de teste, evite estes erros comuns:
- testar comportamento de mock em vez de comportamento real;
- adicionar métodos apenas para teste no código de produção;
- mockar dependências sem entender o acoplamento real.

Se cair em algum desses casos, volte para teste de comportamento real.

## Output esperado

```markdown
## Superpowers TDD

**Target behavior:** [comportamento alvo]

### Phase Results
| Phase | Status | Evidência |
|-------|--------|-----------|
| Phase 1 (RED) | [failing test confirmado] | [erro observado] |
| Phase 2 (GREEN) | [mínimo para passar] | [test run output] |
| Phase 3 (REFACTOR) | [feito|adiado] | [mudanças aplicadas] |
| Phase 4 (PROMOTE) | [regressão coberta] | [suite completa OK] |

### Verification Checklist
- [x/] Cada item do checklist acima

### Next prompt
/superpowers-verification-before-completion
```
