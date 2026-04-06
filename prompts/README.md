# Copilot Prompts Catalog

This folder contains manual entry points for named workflows in VS Code and GitHub Copilot.

Prompts are best when the user needs:

- an explicit slash-command entry point;
- a repeatable manual workflow;
- visible handoff between stages.

## Current prompts

| Prompt | Main purpose |
| --- | --- |
| `super.prompt.md` | route the user through the full Claude/Windsurf-compatible superpowers chain |
| `super-brainstorm.prompt.md` | Claude/Windsurf alias for the brainstorming entry point |
| `super-plan.prompt.md` | Claude/Windsurf alias for the planning entry point |
| `super-subagent.prompt.md` | Claude/Windsurf alias for same-session multi-agent execution |
| `super-execute.prompt.md` | Claude/Windsurf alias for batch execution |
| `super-verify.prompt.md` | Claude/Windsurf alias for the final verification gate |
| `super-tdd.prompt.md` | apply the superpowers TDD discipline for behavior-changing work |
| `super-debug.prompt.md` | run the superpowers debugging flow for bugs and regressions |
| `super-review.prompt.md` | run a structured final code review before delivery or commit |
| `superpowers-start.prompt.md` | start the superpowers chain and route the user to the correct first step |
| `superpowers-brainstorming.prompt.md` | open the brainstorming phase with explicit problem framing |
| `superpowers-writing-plans.prompt.md` | convert exploration into an approved execution plan |
| `superpowers-subagent-driven-development.prompt.md` | execute through coordinator plus workers when multi-agent help is useful |
| `superpowers-executing-plans.prompt.md` | execute an approved plan in controlled batches |
| `superpowers-verification-before-completion.prompt.md` | run the mandatory final verification gate before claiming completion |
| `superpowers-tdd.prompt.md` | run the full TDD discipline (RED → GREEN → REFACTOR → PROMOTE) |
| `superpowers-debugging.prompt.md` | run systematic debugging with 7 phases, hypothesis testing and escalation |
| `superpowers-code-review.prompt.md` | assemble review context and dispatch requester flow for structured code review |
| `superpowers-code-reviewer.prompt.md` | execute dedicated reviewer analysis and return verdict with prioritized findings |
| `ask.prompt.md` | force ORCHESTRATOR_DECISION classification before answering |
| `enforce.prompt.md` | run the strict 12-field orchestrator contract before proceeding |
| `pipeline.prompt.md` | enter the guided pipeline-orchestrator flow in Copilot |
| `skah.prompt.md` | short alias for the agent-customization workflow |

## Scope notes

- Prompts should stay focused on entry and handoff.
- Short `super-*` prompts are compatibility aliases for Claude/Windsurf command names.
- `ask`, `enforce` and `pipeline` are pipeline-orchestrator compatibility entry points backed by explicit parity notes.
- `skah` is a short workspace alias for the `agent-customization` workflow.
- Do not duplicate always-on rules that already belong in instructions.
- Do not turn prompts into giant knowledge-base files.
- Discovery depends on `chat.promptFilesLocations` including this folder.