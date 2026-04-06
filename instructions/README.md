# Copilot Instructions Catalog

This folder contains always-on instruction files for VS Code and GitHub Copilot.

Instructions are best for:

- stable rules that should apply repeatedly;
- lightweight guardrails that do not require manual invocation;
- cross-cutting behavior that should shape execution before detailed prompting.

## Instruction set

| File | Main purpose |
| --- | --- |
| `00-global-copilot-core.instructions.md` | global execution baseline for Copilot work |
| `10-agentic-execution.instructions.md` | guidance on when to use Ask, Plan, Agent, subagents, and sessions |
| `20-context-and-customization.instructions.md` | guidance on context sources and customization layers |
| `30-clean-architecture-and-quality.instructions.md` | minimal-diff, validation, and architecture guardrails |
| `35-superpowers-bootstrap.instructions.md` | bootstrap injection bridge for the superpowers plugin — activates the 1% rule, prompt discipline, tool mapping (Windows: `powershell` override), and native skill layer discovery in every session |
| `40-superpowers-gates.instructions.md` | gate emulation for planning, execution and final verification in the superpowers workflow |
| `50-pipeline-orchestrator-gates.instructions.md` | gate emulation for ORCHESTRATOR_DECISION, routing, batch review and final checks |

## How to think about these files

### `00-global-copilot-core.instructions.md`

Start here. It defines the broad behavioral baseline.

### `10-agentic-execution.instructions.md`

Use this mental model when deciding how work should be delegated or orchestrated.

### `20-context-and-customization.instructions.md`

Use this to avoid mixing instructions, prompts, skills, hooks, and MCP in messy ways.

### `30-clean-architecture-and-quality.instructions.md`

Use this as the quality floor for implementation and validation.

### `40-superpowers-gates.instructions.md`

Use this when the work should preserve the canonical `superpowers` chain inside the Copilot layer.

### `50-pipeline-orchestrator-gates.instructions.md`

Use this when the work should preserve the `pipeline-orchestrator` contract inside the Copilot layer.

## Scope notes

- Instructions should stay short, stable, and low-noise.
- Put enduring rules here, not temporary task notes.
- If a rule only matters for a manual workflow, a prompt or skill is often a better fit.
- Discovery depends on `chat.instructionsFilesLocations` including this folder.

## Recommended order of influence

1. core execution rules
2. agent-selection guidance
3. context and customization strategy
4. quality and architecture guardrails
5. superpowers gate emulation when that workflow applies
6. pipeline-orchestrator gate emulation when that workflow applies

That order helps the agent choose the right surface before it chooses the exact implementation pattern.
