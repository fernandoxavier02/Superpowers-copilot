---
title: VS Code and GitHub Copilot specialist agents
tags:
  - agents
  - vscode
  - github-copilot
  - orchestration
globs:
  - "**"
updated: 2026-03-09
---

## Copilot Agents Catalog

This folder contains reusable custom agents for VS Code and GitHub Copilot.

These agents are meant to:

- specialize tasks that are too broad for a single default chat flow;
- separate orchestration, research, implementation, and review roles;
- stay complementary to instructions, prompts, skills, and MCP;
- remain discoverable without turning the picker into a circus tent.

## How to choose an agent

| If you need to... | Use this agent |
| --- | --- |
| coordinate the canonical `superpowers` workflow | `Superpowers Orchestrator` |
| coordinate the `pipeline-orchestrator` mirror workflow | `Pipeline Orchestrator` |
| coordinate a multi-step Copilot customization effort | `VSCode Copilot Orchestrator` |
| research official VS Code or Copilot documentation | `VSCode Docs Researcher` |
| structure or evolve the knowledge base | `Copilot Architecture Librarian` |
| implement customization files with minimal diff | `Copilot Customization Implementer` |
| configure Remote-SSH, Dev Containers, WSL, tunnels, or Codespaces | `VSCode Remote Expert` |
| review user-facing clarity, naming and discoverability | `copilot-ux-governance-review` skill |
| pressure-test rollout readiness and workflow friction | `release-readiness-for-customizations` skill |

## Quick differences

### `Superpowers Orchestrator` vs `VSCode Copilot Orchestrator`

- Use `Superpowers Orchestrator` when the task is specifically about entering, routing, or preserving the `superpowers` chain.
- Use `VSCode Copilot Orchestrator` for broader Copilot-customization work that is not tied to the canonical `superpowers` workflow.

### `Pipeline Orchestrator` vs `Superpowers Orchestrator`

- Use `Pipeline Orchestrator` when the task is specifically about the Claude/Windsurf `pipeline-orchestrator` mirror and its gates, contracts, routing, or parity boundaries.
- Use `Superpowers Orchestrator` when the task is about the `superpowers` chain.

### `VSCode Copilot Orchestrator` vs `Copilot Customization Implementer`

- Use `VSCode Copilot Orchestrator` to coordinate the workflow and choose who should do what.
- Use `Copilot Customization Implementer` when the implementation path is already clear and the main job is editing the customization artifacts.

### `VSCode Docs Researcher` vs `Copilot Architecture Librarian`

- Use `VSCode Docs Researcher` to gather authoritative source material.
- Use `Copilot Architecture Librarian` to organize that material into reusable knowledge.

### `VSCode Remote Expert` vs project-specific operators

- Use `VSCode Remote Expert` for generic remote-development setup and troubleshooting.
- Use project-specific agents under `.github/agents/` when the request depends on one concrete host, runtime, or operational policy.

### Review skills vs agents

- Use the `copilot-ux-governance-review` skill to inspect discoverability, naming, and end-user clarity.
- Use the `release-readiness-for-customizations` skill for final pressure testing before broader rollout.

## Scope notes

- Agents in `.githubcopilot/agents/` define the main technical team.
- Project-specific operators can live in `.github/agents/` when they depend on repository runtime details.
- Review-oriented artifacts may live as skills when they do not need dedicated agent files.
- Agent discovery depends on `chat.agentFilesLocations` including the relevant folders.
- Prefer an agent only when a prompt, instruction, or skill would not be enough.

## Recommended flow

One common path is:

1. `Superpowers Orchestrator` for canonical chain routing when relevant
2. `Pipeline Orchestrator` when the task is about the pipeline-orchestrator mirror
3. `VSCode Copilot Orchestrator`
4. `VSCode Docs Researcher` when authoritative platform guidance is needed
5. `VSCode Remote Expert` when the problem is remote development or SSH
6. `Copilot Architecture Librarian` or `Copilot Customization Implementer`
7. `copilot-ux-governance-review` and `release-readiness-for-customizations` for final pressure testing

That keeps coordination, research, implementation, and critique in distinct lanes.

