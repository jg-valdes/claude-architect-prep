# Tool Error Handling

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## Four Required Error Context Elements

When a subagent error propagates to the coordinator, it must include:

1. **Failure type** — transient, validation, business, or permission
2. **Attempted action** — the tool, query, and parameters used
3. **Partial results** — any data successfully retrieved before failure (don't discard)
4. **Alternative approaches** — suggestions from subagent domain knowledge

---

## Error Categories

| Category | `isRetryable` | Recovery Strategy |
|---|---|---|
| Transient (timeout, rate limit) | `true` | Retry with exponential backoff |
| Validation (bad format, missing field) | `true` | Fix input and retry |
| Business (policy violation) | `false` | Escalate or alternative workflow |
| Permission (access denied) | `false` | Escalate or get different credentials |

---

## Access Failure vs. Valid Empty Result

This distinction is **heavily tested**:

| Situation | `isError` | Correct Action |
|---|---|---|
| Tool couldn't reach data source (timeout, connection error) | `true` | Consider retry |
| Tool executed successfully, found no matching records | `false` | No retry — this IS the answer |

**Never retry a valid empty result.** A successful query with zero results means the data doesn't exist, not that the tool failed.

---

## Two Critical Anti-Patterns

### Silent Suppression
Returning empty results marked as successful when a timeout occurred. This prevents all recovery because the coordinator believes the search succeeded and found nothing.

### Workflow Termination
Killing the entire pipeline when one subagent fails, discarding all work from successfully-completed subagents.

---

## Multi-Agent Error Propagation

Best practices:
- Subagents perform **local retry** for transient failures before escalating
- Only propagate errors that cannot be locally resolved
- Synthesis outputs should include **coverage annotations** noting which topics are well-supported vs. where gaps exist
- Structured error context enables coordinators to make intelligent recovery decisions

---

## Key Exam Points

- Business errors are **never retryable** — retrying produces identical failures
- Valid empty result ≠ access failure — know the difference
- Silent suppression is **worse** than propagating an error (blocks recovery)
- Always propagate partial results alongside error context
- Local retry before escalating — don't flood coordinator with transient failures

---

## Common Exam Trap

> "A database lookup returns an empty array with `isError: false`. Should the coordinator retry?"

Answer: No — `isError: false` means the tool executed successfully and found no records. Retrying wastes resources and produces the same result.
