<div align="center">
  <img src="assets/fx-studio-ai-logo.png" alt="FX Studio AI" width="600"/>
</div>

<h1 align="center">Superpowers Copilot</h1>

<p align="center">
  <strong>14 Agentic Development Skills for GitHub Copilot</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/platform-VS%20Code%20%2B%20Copilot-007ACC?style=flat-square" alt="Platform"/>
  <img src="https://img.shields.io/badge/license-MIT-green?style=flat-square" alt="License"/>
  <img src="https://img.shields.io/badge/skills-Markdown--based-000000?style=flat-square" alt="Language"/>
</p>

## What It Does

Superpowers Copilot brings the full Superpowers agentic skills framework to GitHub Copilot inside VS Code. All 14 specialized development skills — brainstorming, TDD, code review, debugging, plan execution, subagent delegation, and more — are adapted to work as Copilot Chat participants.

The framework follows the **1% rule methodology**: each skill handles one focused concern, delegates execution via subagents, and produces structured, traceable outputs. This turns Copilot from a code completion tool into a full structured development lifecycle assistant.

## Features

- **14 Specialized Skills** — Brainstorming, TDD, code review, debugging, plan execution, subagent delegation, git worktrees, and more
- **VS Code Native** — Skills work as Copilot Chat participants, fully integrated with the editor
- **Subagent Delegation** — Each skill spawns focused subagents to handle execution details
- **1% Rule Methodology** — One concern per step, structured and auditable
- **Structured Development Lifecycle** — Ideation, specification, implementation, review, and deployment
- **Markdown-Based Skills** — No compiled code required; skills are defined as structured Markdown templates

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/fernandoxavier02/Superpowers-copilot.git
   ```

2. Navigate to the project directory:
   ```bash
   cd Superpowers-copilot
   ```

3. Open the project in VS Code and ensure GitHub Copilot Chat is installed and active.

4. Follow the setup guide to register the skills as Copilot Chat participants.

## Usage

Invoke skills directly in Copilot Chat:

```
# Brainstorm a feature
@brainstorm Add real-time notifications to the dashboard

# Run TDD workflow
@tdd Implement input validation for the signup form

# Code review
@review Check the auth module for security issues

# Debug a problem
@debug Users are getting 403 errors after login
```

Each skill guides you through a structured workflow, producing clear deliverables and next steps.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

<div align="center">
  <strong>Built by <a href="https://github.com/fernandoxavier02">Fernando Xavier</a></strong>
  <br/>
  <a href="https://fxstudioai.com">FX Studio AI</a> — Business Automation with AI
</div>
