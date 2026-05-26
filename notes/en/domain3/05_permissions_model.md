# Permissions Model

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## What Is the Permissions Model?

Claude Code asks for user approval before running potentially risky tools. The permissions model lets you pre-approve (allow) or block (deny) specific tools and commands so Claude doesn't pause to ask every time.

---

## Configuration

Permissions live in `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Edit",
      "Write",
      "Bash(npm run *)",
      "Bash(git *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

---

## Glob Pattern Syntax

For `Bash` commands, you can match specific commands with globs:

```
"Bash(npm *)"          → any npm command
"Bash(npm run *)"      → only npm run commands
"Bash(git diff)"       → only exactly `git diff`
"Bash(git *)"          → any git command
"Read"                 → allow Read tool (no glob needed for non-Bash tools)
```

---

## Permission Resolution Order

When Claude wants to use a tool:

1. Check **deny** list — if matched, **block** (deny always wins)
2. Check **allow** list — if matched, **allow without prompting**
3. Neither matched → **prompt the user**

**Deny takes priority over allow** — if a command matches both, it's denied.

---

## Settings File Scope

| File | Scope | Version controlled? |
|---|---|---|
| `~/.claude/settings.json` | Global (all projects) | No |
| `.claude/settings.json` | Project (shared) | Yes |
| `.claude/settings.local.json` | Local override (personal) | No |

**Priority:** local > project > global

---

## Tool Names Reference

| Tool | What it does |
|---|---|
| `Read` | Read files |
| `Edit` | Edit files (diff-based) |
| `Write` | Write/create files |
| `Bash` | Run shell commands |
| `WebSearch` | Search the web |
| `WebFetch` | Fetch a URL |
| `Agent` | Spawn subagents |

---

## Key Exam Points

- **Deny always wins** — if a command is in both allow and deny, it's denied
- Permissions are **additive across levels** — project adds to global, local adds to project
- `Bash(npm *)` and `Bash(npm run *)` are **different** — the second is more restrictive
- Without any allow rules, Claude prompts for permission on every tool use
- In CI/CD, use `--allowedTools` flag instead of settings.json for per-command control

---

## Common Trap on the Exam

> "A project settings.json has `"allow": ["Bash(git *)"]` and the global settings.json has `"deny": ["Bash(git push *)"]`. What happens when Claude tries to run `git push`?"

Answer: **Denied.** Deny rules take priority over allow rules, and all levels are combined — the global deny blocks the project allow.
