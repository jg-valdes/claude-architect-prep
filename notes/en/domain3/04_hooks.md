# Hooks

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## What Are Hooks?

Hooks are shell commands that Claude Code automatically executes in response to specific events — before or after tool use, on stop, etc. They let you automate reactions to Claude's actions without writing it into CLAUDE.md.

---

## Hook Events

| Event | Fires when... |
|---|---|
| `PreToolUse` | Before Claude calls any tool |
| `PostToolUse` | After a tool call completes |
| `Notification` | When Claude sends a notification |
| `Stop` | When Claude finishes a response |

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
          {
            "type": "command",
            "command": "echo 'About to run a bash command'"
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write",
        "hooks": [
          {
            "type": "command",
            "command": "npm run lint -- --fix"
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude finished'"
          }
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
| `~/.claude/settings.json` | Global — applies to all projects |
| `.claude/settings.json` | Project — checked into repo, shared with team |
| `.claude/settings.local.json` | Local override — not checked in, personal |

**Priority:** local > project > global (same pattern as CLAUDE.md)

---

## The `matcher` Field

The `matcher` filters which tool triggers the hook:

```json
"matcher": "Bash"        // only triggers on Bash tool calls
"matcher": "Write"       // only triggers on Write tool calls
"matcher": "Bash(npm *)" // only triggers on bash commands matching the glob
```

Omit `matcher` entirely to trigger on **all** tool calls of that event type.

---

## Practical Hook Use Cases

- **Auto-lint on file write:** Run `eslint --fix` every time Claude writes a JS/TS file
- **Auto-format:** Run `prettier` after every `Edit` or `Write`
- **Logging:** Log all Bash commands Claude runs for audit trail
- **Notifications:** Send a desktop notification when Claude finishes a long task
- **Blocking dangerous commands:** Use `PreToolUse` to intercept and reject risky operations

---

## Key Exam Points

- Hooks are configured in **`settings.json`**, not in `CLAUDE.md`
- `PreToolUse` can **block** a tool call by exiting with a non-zero code
- `PostToolUse` runs **after** the tool, so it can't prevent the action — only react to it
- The `matcher` field uses the same **glob pattern** syntax as permissions
- Project hooks (`.claude/settings.json`) are **shared with the team**; local hooks (`.claude/settings.local.json`) are personal

---

## Common Trap on the Exam

> "You want to automatically run `prettier` every time Claude edits a file. Where do you configure this?"

Answer: As a `PostToolUse` hook with `"matcher": "Edit"` in `.claude/settings.json` — **not** in CLAUDE.md (CLAUDE.md is instructions for Claude to follow, not automated shell triggers).
