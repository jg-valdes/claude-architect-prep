# Custom Slash Commands & Skills

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## Creating Custom Commands

Commands created in `.claude/skills/` or `.claude/commands/` produce identical `/commands` that developers can invoke. Both directory names work.

### Scoping Levels

| Location | Scope | Version-Controlled? |
|---|---|---|
| `.claude/skills/` or `.claude/commands/` | Project — all team members | Yes |
| `~/.claude/skills/` or `~/.claude/commands/` | Personal — not shared | No |

---

## Three Critical Frontmatter Options

```yaml
---
context: fork
allowed-tools: Read, Grep
argument-hint: "filename to analyze"
---
```

| Option | Effect |
|---|---|
| `context: fork` | Runs skill in isolated sub-agent context — verbose output doesn't clutter main conversation |
| `allowed-tools` | Restricts which tools the skill can use (security boundary) |
| `argument-hint` | Prompts the developer for required parameters when invoked without arguments |

---

## Skills vs. CLAUDE.md

| Skills | CLAUDE.md |
|---|---|
| On-demand, task-specific workflows | Always-loaded, universal standards |
| Loaded only when invoked | Applied to every session |
| Use for: "run a review", "generate a report" | Use for: naming conventions, error handling standards |

**Anti-pattern:** Placing task-specific procedures in CLAUDE.md.

---

## Path-Specific Rules (`.claude/rules/`)

Rule files use YAML frontmatter with a `paths` field specifying which files trigger loading:

```yaml
---
paths: ["terraform/**/*"]
---
# Terraform Conventions
- Use snake_case for resource names
- Tag every resource with environment and team labels
```

**Key advantage:** Glob patterns like `**/*.test.tsx` catch test files across the entire codebase, regardless of directory depth.

### When to Use Each Approach

| Scenario | Best Approach |
|---|---|
| Universal team standards | Root CLAUDE.md |
| Single package directory | Directory-level CLAUDE.md |
| File type across many directories | Path-specific rules with glob patterns |
| On-demand task workflows | Skills in `.claude/skills/` |

---

## Token Efficiency

Path-scoped rules load **only when editing matching files**. Root CLAUDE.md loads for every session. For large projects with multiple convention categories, path-scoped rules preserve token budget.

---

## Key Exam Points

- Project-scoped commands → `.claude/skills/` (shared via git)
- Personal commands → `~/.claude/skills/` (not version-controlled)
- `context: fork` prevents verbose skill output from polluting main conversation
- Skills are **on-demand**; CLAUDE.md is **always-on**
- Path-specific rules in `.claude/rules/` with `paths` frontmatter = file-type conventions

---

## Common Exam Traps

- Confusing user-scoped paths (`~/.claude/`) with project-scoped (`.claude/`) for team commands
- Treating skills as always-on guidance like CLAUDE.md
- Forgetting `context: fork` for verbose outputs
- Placing task-specific workflows in CLAUDE.md instead of skills
- Choosing directory-level CLAUDE.md over path-specific rules when conventions apply across 50+ locations
