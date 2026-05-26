# CI/CD Integration with Claude Code

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## Non-Interactive Mode

Claude Code is designed to run in automated pipelines. The key is using **non-interactive mode** so it doesn't prompt for input or show a UI.

```bash
claude --print "review this PR for security issues"
claude -p "generate a test for the function in src/utils.ts"
```

`--print` (or `-p`) tells Claude to:
- Output the result to stdout
- Exit immediately when done
- Never ask for user input

---

## Key CLI Flags for CI/CD

| Flag | Purpose |
|---|---|
| `--print` / `-p` | Non-interactive mode, output to stdout |
| `--model` | Specify which model to use (e.g. `claude-opus-4-7`) |
| `--allowedTools` | Whitelist specific tools Claude can use |
| `--system-prompt` | Override the system prompt |
| `--no-interactive` | Same effect as `--print` |

### Example: Restricting tools in CI
```bash
claude -p "review the diff for bugs" \
  --allowedTools "Read,Bash(git diff)" \
  --model claude-sonnet-4-6
```

---

## Common CI/CD Use Cases

### Automated PR review
```yaml
# GitHub Actions example
- name: Claude Code Review
  run: |
    claude -p "Review the git diff for bugs and security issues. 
    Output a markdown summary." \
    --allowedTools "Read,Bash(git *)"
```

### Test generation on commit
```bash
claude -p "Generate unit tests for any new functions added in the last commit" \
  --allowedTools "Read,Write,Bash(git show HEAD)"
```

### Automated documentation
```bash
claude -p "Update CHANGELOG.md based on commits since last tag" \
  --allowedTools "Read,Write,Bash(git log *)"
```

---

## Settings for CI Environments

In CI you typically want to pre-approve tools so Claude doesn't pause waiting for permission. Use `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Bash(git *)",
      "Bash(npm run *)"
    ]
  }
}
```

Or pass `--allowedTools` inline to keep it explicit per-command.

---

## Key Exam Points

- `--print` / `-p` is **required** for non-interactive/CI use — without it Claude will open the interactive UI
- `--allowedTools` is the correct way to **restrict tool access** in automated contexts
- Claude Code can be used in **any CI system** (GitHub Actions, GitLab CI, Jenkins, etc.) — it's just a CLI
- Always restrict tools to the **minimum needed** for the task (principle of least privilege)

---

## Common Trap on the Exam

> "You want Claude Code to run in a GitHub Actions pipeline without user interaction. Which flag is required?"

Answer: `--print` (or `-p`). Without it, Claude Code enters interactive mode and the pipeline will hang.
