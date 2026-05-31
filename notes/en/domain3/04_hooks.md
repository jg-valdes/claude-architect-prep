# Hooks

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## What Are Hooks?

Hooks are shell commands that Claude Code automatically executes in response to specific lifecycle events — before or after tool use, on stop, etc. They enforce behavior **deterministically**, unlike CLAUDE.md instructions which are probabilistic.

---

## Hook Events

| Event | Fires when... | Can block? |
|---|---|---|
| `PreToolUse` | Before Claude calls any tool | Yes (exit non-zero) |
| `PostToolUse` | After a tool call completes | No |
| `Notification` | When Claude sends a notification | No |
| `Stop` | When Claude finishes a response | No |

---

## Configuration

Hooks are defined in `settings.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command", "command": "echo 'About to run bash'" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          { "type": "command", "command": "npm run lint -- --fix" }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          { "type": "command", "command": "notify-send 'Claude finished'" }
        ]
      }
    ]
  }
}
```

---

## Settings File Locations

| File | Scope |
|---|---|
| `~/.claude/settings.json` | Global — all projects |
| `.claude/settings.json` | Project — shared with team via git |
| `.claude/settings.local.json` | Local override — personal, not committed |

**Priority:** local > project > global

---

## The `matcher` Field

```json
"matcher": "Bash"          // triggers on any Bash tool call
"matcher": "Write"         // triggers on Write tool calls
"matcher": "Bash(npm *)"   // triggers on bash commands matching glob
```

Omit `matcher` to trigger on **all** tool calls of that event type.

---

## Hooks vs. CLAUDE.md

| Hooks | CLAUDE.md |
|---|---|
| Configured in `settings.json` | Configuration instructions for Claude |
| Executed by the **harness** (deterministic) | Followed by Claude (probabilistic) |
| Automated shell triggers | Behavioral guidance |
| Cannot be ignored by the model | Can occasionally be missed |

---

## Practical Use Cases

- **Auto-lint on file write:** Run `eslint --fix` every time Claude writes a JS/TS file
- **Auto-format:** Run `prettier` after every `Edit` or `Write`
- **Logging:** Log all Bash commands for audit trail
- **Notifications:** Desktop notification when Claude finishes
- **Blocking dangerous commands:** `PreToolUse` to intercept and reject risky operations

---

## Key Exam Points

- `PreToolUse` can **block** a tool call (exit non-zero)
- `PostToolUse` cannot prevent the action — only react to it
- Hooks live in `settings.json`, **not** CLAUDE.md
- Project hooks (`.claude/settings.json`) are shared with team
- Local hooks (`.claude/settings.local.json`) are personal

---

## Common Exam Trap

> "You want to automatically run `prettier` every time Claude edits a file. Where do you configure this?"

Answer: `PostToolUse` hook with `"matcher": "Edit"` in `.claude/settings.json` — **not** in CLAUDE.md (CLAUDE.md is instructions for Claude to follow, not automated shell triggers).
