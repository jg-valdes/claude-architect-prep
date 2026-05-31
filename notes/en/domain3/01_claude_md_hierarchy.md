# CLAUDE.md Hierarchy

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## The Three-Level Hierarchy

| Level | Location | Version-Controlled? | Scope |
|---|---|---|---|
| User | `~/.claude/CLAUDE.md` | No | Only that developer |
| Project | `.claude/CLAUDE.md` or root `CLAUDE.md` | Yes | All team members |
| Directory | `packages/api/CLAUDE.md` (any subdirectory) | Yes | That directory only |

---

## Loading Order and Conflict Handling

Files are **concatenated into context**, not overriding each other. Loading goes from broadest to most specific scope — content closer to the working directory is read last.

Within a directory, `CLAUDE.local.md` appends **after** `CLAUDE.md`.

**Critical:** "If two rules contradict each other, Claude may pick one arbitrarily." CLAUDE.md provides guidance without strict enforcement.

For **guaranteed compliance** → use `settings.json` (client-enforced) or hooks (lifecycle-bound).

---

## Modular Organisation with @ Path Imports

Large CLAUDE.md files can be split using the `@` syntax (no `@import` keyword needed):

```markdown
# .claude/CLAUDE.md
Coding standards:
@./standards/naming-conventions.md
@./standards/error-handling.md
@./standards/testing-requirements.md
```

Files load **eagerly** — referenced files are inlined at load time. This keeps source maintenance clean but **does not** reduce session context size.

---

## CLAUDE.local.md

- Loads after `CLAUDE.md` at the same level
- Conventionally gitignored for personal tweaks
- For team rules, use `CLAUDE.md` instead

---

## The `.claude/rules/` Directory

Alternative to single CLAUDE.md — holds topic-specific files like `testing.md`, `api-conventions.md`, `deployment.md`. Files can include YAML frontmatter for path scoping (see path-specific rules).

---

## The `/memory` Command

`/memory` **reveals which configuration files are already loaded**. It does NOT load configuration files.

Configuration loads automatically at session start — `/memory` is a diagnostic tool, not an activation mechanism.

---

## Key Exam Points

- New team member not receiving instructions → configuration is in **user-level**, not project-level
- User-level settings don't sync via git — they stay on one machine
- `@` imports inline content eagerly — doesn't save context budget
- Contradicting rules → Claude picks arbitrarily; use `settings.json` for guaranteed enforcement
- `/memory` = diagnose what's loaded, not activate it

---

## Common Exam Trap

> "New developers clone the repo but get different Claude Code behavior than the senior engineer."

Answer: The senior engineer's conventions are in `~/.claude/CLAUDE.md` (user-level), not in `.claude/CLAUDE.md` (project-level). Fix: move to project-level so it syncs via git.
