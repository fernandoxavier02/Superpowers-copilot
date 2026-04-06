# Copilot CLI Tool Mapping — Windows Override

> **Windows-specific supplement** to `copilot-tools.md`.  
> The SSOT (`copilot-tools.md`) references `bash` for shell commands.  
> On Windows with Copilot CLI, use `powershell` instead.

## Shell command mapping (Windows)

| `copilot-tools.md` says | Windows Copilot CLI uses |
|-------------------------|--------------------------|
| `bash` (run commands) | `powershell` |
| `bash` with `async: true` | `powershell` with `mode="async"` |
| `write_bash` | `write_powershell` |
| `read_bash` | `read_powershell` |
| `stop_bash` | `stop_powershell` |
| `list_bash` | `list_powershell` |

## Path conventions (Windows)

- Use backslashes: `C:\Users\ferna\` or escaped forward slashes in JSON: `C:/Users/ferna/`
- Global profile root: `C:\Users\ferna\.githubcopilot-global\`
- No `~` expansion in VS Code `settings.json` — use absolute paths

## Authority

`instructions/35-superpowers-bootstrap.instructions.md` is the authoritative Windows tool mapping override and is injected every session. This file provides the supplemental reference for skills that read `copilot-tools.md`.

All other tool mappings in `copilot-tools.md` (view, create, edit, grep, glob, skill, web_fetch, task, sql, etc.) apply unchanged on Windows.
