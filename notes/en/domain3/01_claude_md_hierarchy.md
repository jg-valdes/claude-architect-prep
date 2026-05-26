# CLAUDE.md Hierarchy

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## What is CLAUDE.md?

`CLAUDE.md` is the primary configuration file for Claude Code. It provides persistent context and instructions that Claude reads at the start of every session. Think of it as a "project README for Claude."

---

## File Locations (Hierarchy)

Claude reads and merges CLAUDE.md files from multiple levels:

```
~/.claude/CLAUDE.md                  ← 1. Global (all projects)
{project_root}/CLAUDE.md             ← 2. Project-level
{project_root}/.claude/CLAUDE.md     ← 2. Also project-level
{project_root}/{subdir}/CLAUDE.md    ← 3. Subdirectory (local override)
```

**Priority rule:** More specific = higher priority.  
A subdirectory `CLAUDE.md` overrides the project one. A project one overrides global.

All files are **merged**, not replaced — so global instructions still apply unless explicitly contradicted.

---

## What to Put in CLAUDE.md

### Commands
```markdown
## Build & Test
- Build: `npm run build`
- Test: `npm test`
- Lint: `npm run lint`
```

### Conventions
```markdown
## Code Style
- Use TypeScript strict mode
- Prefer `const` over `let`
- No `any` types
```

### Architecture notes
```markdown
## Architecture
- Auth is handled in `src/middleware/auth.ts`
- Never write directly to the DB — always use the repository layer
```

### Behavioral instructions
```markdown
## Instructions for Claude
- Always run tests before marking a task complete
- Never modify migration files
- Ask before deleting any file
```

---

## Key Exam Points

- `CLAUDE.md` is **loaded automatically** — you don't reference it in prompts
- Multiple files are **merged**, with more specific files taking precedence
- It's meant to be **checked into version control** so the whole team benefits
- Global `~/.claude/CLAUDE.md` is for personal preferences that apply everywhere
- Subdirectory `CLAUDE.md` files only activate when Claude is working **within that directory**

---

## Common Trap on the Exam

> "Which CLAUDE.md takes precedence when there is a conflict?"

Answer: **The most specific one** (subdirectory > project > global). But remember — non-conflicting instructions from all levels are still merged and applied.
