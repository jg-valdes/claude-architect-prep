# Permissions Model & Plan Mode

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## Permissions Model

Permissions control which tools Claude Code can use. They are configured in `settings.json`.

### Allow vs. Deny

```json
{
  "permissions": {
    "allow": ["Bash(git *)", "Read", "Write"],
    "deny": ["Bash(rm *)", "Bash(sudo *)"]
  }
}
```

**Precedence:** `deny` rules take priority over `allow` rules when they overlap.

### `--allowedTools` Flag

Passed at invocation time to limit tools for that session:

```bash
claude --allowedTools "Read,Grep,Bash(git log *)" -p "Summarize recent changes"
```

Acts as an **allowlist** — only the listed tools are available. All others are implicitly denied.

---

## Plan Mode vs. Direct Execution

### The Core Distinction

**The decision is not about difficulty — it's about ambiguity.**

A difficult but well-defined bug fix (clear stack trace, single function, known cause) → **direct execution**.

A straightforward-seeming but ambiguous multi-file restructuring → **plan mode**.

---

### When to Use Plan Mode

- Large-scale architectural changes (monolith restructuring, module reorganization)
- Tasks with multiple valid implementation approaches
- Multi-file modifications (45+ files) needing consistent strategy
- Codebase exploration necessary before changes
- Situations where architectural decisions have downstream consequences

> Plan mode enables safe exploration and design. Claude reads the codebase, analyses dependencies, and proposes an approach — **all without modifying any files**.

---

### When to Use Direct Execution

- Well-scoped changes with clear stack traces
- The correct approach is already known
- Limited-scope modifications (single function, one file)

---

### The Hybrid Pattern (Plan-Then-Execute)

1. Explore and design in plan mode
2. Implement using direct execution with strategy already determined

---

### The Explore Subagent

Isolates verbose discovery output, keeping the main conversation context clean for focused implementation work.

---

## Decision Framework

| Task Type | Recommended Mode |
|---|---|
| Architectural restructuring | Plan mode |
| Library migration (many files) | Plan mode, then direct |
| Single-file bug fix (clear stack trace) | Direct execution |
| Unknown codebase, open-ended task | Plan mode |

---

## Key Exam Points

- `deny` rules take priority over `allow` rules
- `--allowedTools` is an **allowlist** at invocation time
- Plan vs. direct = **ambiguity**, not difficulty
- Plan mode → no file modifications — safe exploration only
- Hybrid pattern: plan → direct is the production best practice

---

## Common Exam Traps

- Defaulting to direct execution for multi-file architectural changes (needs plan mode)
- Over-planning simple, single-file fixes with clear requirements
- Missing the hybrid plan-then-execute pattern
- Treating task difficulty as the deciding factor (it's ambiguity that matters)
