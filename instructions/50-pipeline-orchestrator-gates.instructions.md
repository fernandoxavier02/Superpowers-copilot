---
name: Pipeline Orchestrator Gate Emulation
description: Guardrails always-on para preservar entry gate, classificacao disciplinada, handoffs e checks finais do pipeline-orchestrator no GitHub Copilot.
applyTo: "**"
---
# Pipeline-orchestrator gates
- Se o trabalho segue o workflow `pipeline-orchestrator`, emitir ou exigir `ORCHESTRATOR_DECISION` antes de analise aprofundada, planejamento ou implementacao nao trivial.
- Usar o shape semantico adotado no mirror: `solicitacao`, `tipo`, `complexidade`, `severidade`, `persona`, `arquivos_provaveis`, `tem_spec`, `execucao`, `pipeline_selecionado`, `fluxo`, `riscos`, `informacao_completa`, `lacunas`.
- Manter ownership explicito: tipo/persona/severidade no orchestrator; complexidade no classifier; selecao de pipeline no documenter.
- Se faltar informacao, parar o fluxo com `informacao_completa: false`, listar `lacunas` e fazer exatamente uma pergunta de clarificacao por vez em vez de inventar.
- Se o trabalho nao for trivial e nao houver plano aprovado, nao avancar para execucao; fazer handoff para `/superpowers-writing-plans`.
- Se houver mudanca de comportamento ou necessidade clara de protecao contra regressao, preferir `/superpowers-tdd` antes do codigo.
- Em trabalho por batch, nao avancar sem registrar: o que foi feito, findings adversariais, correcoes aplicadas e status de validacao.
- Quando um batch gerar implementacao auditavel, recomendar `/superpowers-code-review` antes da conclusao final.
- Nao declarar conclusao final sem checks estruturados equivalentes ao `PA_DE_CAL` ou `FINAL_VALIDATOR_RESULT`.
- Quando o runtime nao enforce o gate sozinho, tratar isso como `guided parity` e dizer isso claramente.
- Consultar `../knowledge-base/20-pipeline-orchestrator-canonical-baseline.md`, `../knowledge-base/21-pipeline-orchestrator-copilot-compatibility-matrix.md`, `../knowledge-base/25-pipeline-orchestrator-routing-and-handoffs.md` e `../knowledge-base/26-pipeline-orchestrator-gate-emulation.md`.