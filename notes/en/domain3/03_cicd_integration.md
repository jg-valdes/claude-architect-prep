# CI/CD Integration with Claude Code

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## The `-p` / `--print` Flag

**The single most directly testable fact in Domain 3.**

Without `-p`, Claude Code hangs in CI pipelines waiting for keyboard input. The flag enables non-interactive execution — processes the prompt and exits automatically.

```bash
claude -p "Review these changes for security issues" --output-format json
```

---

## Structured Output Flags

Two flags work together for machine-parseable output:

| Flag | Purpose |
|---|---|
| `--output-format json` | Emit JSON instead of prose |
| `--json-schema <file>` | Validate output against a schema |

These enable automated systems to:
- Post inline PR comments
- Filter findings by severity
- Track issues across runs

---

## Session Context Isolation

> "The same Claude session that generated code is less effective at reviewing its own changes."

The session retains reasoning context from code generation — leading to confirmation bias in review. Independent review instances evaluate code **without prior justification bias**.

**Correct approach:** Launch separate Claude Code instances for generation and review.

---

## Incremental Review Context

Automated reviews should track previous findings and report **only new or unaddressed issues**.

Without this: duplicate comments appear on every push, eroding developer trust.

---

## CLAUDE.md for CI

Project context files provide CI-invoked Claude Code with:
- Testing standards and available fixtures
- Review criteria and focus areas
- Existing test coverage information

This prevents low-value boilerplate generation in automated contexts.

---

## Synchronous API vs. Batch API

| Use Case | API | Latency |
|---|---|---|
| Pre-merge checks (developers wait) | Synchronous | Real-time |
| Overnight reports, weekly audits | Batch API | Up to 24 hours |
| Agentic workflows with tool calls | Synchronous | N/A (Batch can't do this) |

**Batch API savings:** 50% cost reduction — but use only for latency-tolerant workflows.

**Critical:** The Batch API cannot support multi-turn tool calling within a single request.

---

## Key Exam Points

- `-p` flag = non-interactive mode — **required** for all CI usage
- `--output-format json` + `--json-schema` = machine-parseable output
- Self-review (same session) < independent instance review
- Batch API = 50% cheaper but up to 24h latency and **no** tool calling
- Batch API is **wrong** for blocking pre-merge workflows

---

## Common Exam Traps

- Fixing a hanging CI job with environment variables or stdin redirection (it's the missing `-p` flag)
- Assuming the same session reviews its own output as effectively as an independent instance
- Using the Batch API for pre-merge checks where developers are waiting
- Not tracking previous findings, causing duplicate comments on every push
