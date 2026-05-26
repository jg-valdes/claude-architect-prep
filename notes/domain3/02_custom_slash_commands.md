# Custom Slash Commands

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## What Are Custom Slash Commands?

Custom slash commands let you save reusable prompts as files that can be invoked with `/command-name` in Claude Code. They're useful for repetitive tasks like running deployments, generating PR descriptions, or running code reviews.

---

## File Structure

Commands are stored as Markdown files:

```
.claude/commands/           ← Project-level commands (shared with team)
  deploy.md                 → /deploy
  run-tests.md              → /run-tests
  review-pr.md              → /review-pr

~/.claude/commands/         ← Global commands (personal, all projects)
  my-template.md            → /my-template
```

**Naming rule:** The filename (without `.md`) becomes the slash command name.

---

## Command File Format

A command file is just a Markdown file containing the prompt Claude will execute:

```markdown
# Deploy to Environment

Deploy the current branch to $ARGUMENTS. Before deploying:
1. Run `npm run build` and confirm it passes
2. Run `npm test` and confirm all tests pass
3. Show me the git diff against main
4. Ask for confirmation before proceeding
```

Invoked as: `/deploy staging` or `/deploy production`

---

## Using `$ARGUMENTS`

`$ARGUMENTS` is a special placeholder that captures everything typed after the command name.

```bash
/deploy staging       → $ARGUMENTS = "staging"
/review-pr 42         → $ARGUMENTS = "42"
/generate-test utils  → $ARGUMENTS = "utils"
```

If no arguments are passed, `$ARGUMENTS` is an empty string.

---

## Scope: Project vs Global

| Location | Scope | Use case |
|---|---|---|
| `.claude/commands/` | Project only | Team-shared workflows, repo-specific tasks |
| `~/.claude/commands/` | All projects | Personal productivity commands |

Project commands take precedence over global if names conflict.

---

## Key Exam Points

- Commands are just **Markdown files** — no special syntax beyond `$ARGUMENTS`
- They execute as **prompts** — Claude reads the file content and acts on it
- `.claude/commands/` should be **checked into version control** for team sharing
- `~/.claude/commands/` is **personal** and not shared
- The command name comes from the **filename**, not from content inside the file

---

## Common Trap on the Exam

> "Where should you put a slash command that all team members should have access to?"

Answer: `.claude/commands/` (project-level, checked into the repo) — **not** `~/.claude/commands/` which is personal/global.
