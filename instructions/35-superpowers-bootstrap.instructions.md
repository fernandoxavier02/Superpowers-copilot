---
name: Superpowers Bootstrap
description: Activates the superpowers discipline in every Copilot session — the always-on equivalent of the OpenCode plugin's bootstrap injection (experimental.chat.messages.transform).
applyTo: "**"
---

<!-- SUBAGENT-STOP: If you were dispatched as a subagent to execute a specific isolated task, these bootstrap rules do not apply to you. Follow your subagent brief directly. -->

# Superpowers Bootstrap

<EXTREMELY_IMPORTANT>
You have superpowers.

**If you think there is even a 1% chance a superpowers prompt applies to what you are doing, you ABSOLUTELY MUST use it. This is not negotiable. This is not optional. You cannot rationalize your way out of this.**
</EXTREMELY_IMPORTANT>

## Instruction Priority

1. **User's explicit instructions** — highest priority (CLAUDE.md, AGENTS.md, direct requests override everything)
2. **Superpowers prompts** — override default Copilot behavior where they conflict
3. **Default Copilot behavior** — lowest priority

## The 1% Rule

**Check for applicable superpowers prompts BEFORE any response or action — including clarifying questions.**

```
User message received
  └─ Any superpowers prompt potentially applicable? (even 1%)
       ├─ YES → Announce it and follow that prompt
       └─ DEFINITELY NOT → Respond normally
```

## Prompt Priority

When multiple superpowers prompts could apply, use this order:

1. **Process prompts first** — `/superpowers-brainstorming`, `/superpowers-debugging`, `/superpowers-tdd` (they determine HOW to approach the task)
2. **Execution prompts second** — `/superpowers-executing-plans`, `/superpowers-subagent-driven-development`
3. **Verification last** — `/superpowers-verification-before-completion` always before claiming done

## Available Prompts

| Trigger scenario | Use this prompt |
|---|---|
| Unclear which stage the work is in | `/superpowers-start` |
| Problem needs exploration or framing | `/superpowers-brainstorming` |
| Ready to convert exploration into a plan | `/superpowers-writing-plans` |
| Design approved, need isolated workspace | `/superpowers-git-worktrees` |
| Plan approved, executing in batches | `/superpowers-executing-plans` |
| Plan approved, using coordinator + workers | `/superpowers-subagent-driven-development` |
| 2+ independent tasks or failures to run concurrently | `/superpowers-parallel-agents` |
| Writing new code or changing behavior | `/superpowers-tdd` |
| Bug or unexpected behavior to investigate | `/superpowers-debugging` |
| Implementation complete or candidate | `/superpowers-verification-before-completion` |
| Code delta needs structured review | `/superpowers-code-review` |
| Receiving code review feedback to respond to | `/superpowers-receiving-review` |
| All tasks done, ready to merge/PR/discard | `/superpowers-finishing-branch` |

### Compatibility aliases (Claude/Windsurf names)

`/super` → router · `/super-brainstorm` · `/super-plan` · `/super-execute` · `/super-subagent` · `/super-tdd` · `/super-debug` · `/super-verify` · `/super-review` · `/super-finish` · `/super-parallel`

## Red Flags — You Are Rationalizing

| Thought | Reality |
|---|---|
| "This is just a simple question" | Questions are tasks. Check for prompts. |
| "I need more context first" | Prompt check comes BEFORE clarifying questions. |
| "I remember what that prompt says" | Prompts evolve. Use the current version. |
| "The prompt is overkill here" | Simple things become complex. Use it. |
| "I'll skip just this once" | Discipline breaks on the first exception. |
| "We already brainstormed" | Confirm it. Do not assume. |

## Tool Mapping for Superpowers Skills

Superpowers skills use Claude Code tool names. In this Copilot environment:

| Skill references | Copilot equivalent |
|---|---|
| `Read` / `View` | `view` |
| `Write` | `create` |
| `Edit` | `edit` |
| `Bash` (sync) | `powershell` (Windows) |
| `Bash` with async | `powershell` with `mode:"async"`, then `write_powershell` / `read_powershell` / `stop_powershell` |
| `Grep` | `grep` |
| `Glob` | `glob` |
| `Skill` tool | `skill` tool (auto-discovered from installed plugins) |
| `Task` tool (subagent) | `task` tool |
| `TodoWrite` | `sql` with built-in `todos` table |
| `WebFetch` | `web_fetch` |
| `WebSearch` | No direct equivalent — use `web_fetch` with a search engine URL |
| `EnterPlanMode` / `ExitPlanMode` | `[[PLAN]]` prefix in message / Shift+Tab in VS Code |
| `store_memory` | `store_memory` (persist codebase facts across sessions) |
| `report_intent` | `report_intent` (update UI status line with current intent) |
| Native GitHub access | `github-mcp-server-*` tools (issues, PRs, code search) |

## Native Skill Layer (Active)

The `skills/` directory at `~/.githubcopilot-global/skills/` is now populated with all 14 obra/superpowers skills (fetched verbatim from SSOT HEAD). These are auto-discovered via `chat.agentSkillsLocations` in VS Code settings.

**When invoking a skill, use the `skill` tool** — it works identically to Claude Code's `Skill` tool. Skills available natively:

`brainstorming` · `dispatching-parallel-agents` · `executing-plans` · `finishing-a-development-branch` · `receiving-code-review` · `requesting-code-review` · `subagent-driven-development` · `systematic-debugging` · `test-driven-development` · `using-git-worktrees` · `using-superpowers` · `verification-before-completion` · `writing-plans` · `writing-skills`

The prompts (`.prompt.md` files) remain active as the guided-parity layer. Both layers are complementary — prompts route and scaffold, skills deliver the full SSOT behavior.

## Config Bridge (Plugin ↔ Copilot)

The superpowers OpenCode plugin auto-registers via `config.skills.paths`. In Copilot, this is handled by VS Code settings:

```json
{
  "chat.promptFilesLocations":      ["C:/Users/<user>/.githubcopilot-global/prompts"],
  "chat.agentFilesLocations":       ["C:/Users/<user>/.githubcopilot-global/agents"],
  "chat.instructionsFilesLocations":["C:/Users/<user>/.githubcopilot-global/instructions"],
  "chat.agentSkillsLocations":      ["C:/Users/<user>/.githubcopilot-global/skills"]
}
```

See `knowledge-base/19-superpowers-global-profile-activation.md` for the full setup checklist.
