# Error Propagation in Multi-Agent Systems

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## Four Required Error Context Elements

When a subagent error propagates to the coordinator, it must include:

1. **Failure type** — transient / validation / business / permission
2. **Attempted action** — specific tool, query, and parameters used
3. **Partial results** — data successfully retrieved before failure (don't discard)
4. **Alternative approaches** — suggestions from subagent domain knowledge

---

## Two Critical Anti-Patterns

### Silent Suppression
Returning empty results marked as successful when a timeout occurred.

**Why it's harmful:** Prevents all recovery — coordinator believes the search succeeded and found nothing. No retry, no alternative approach.

### Workflow Termination
Killing the entire pipeline when one subagent fails.

**Why it's harmful:** Discards all work from successfully-completed subagents.

---

## Access Failure vs. Valid Empty Result

| Situation | Correct Classification | Action |
|---|---|---|
| Tool couldn't reach data source (timeout, connection error) | Access failure — `isError: true` | Retry locally; escalate if persistent |
| Tool executed successfully, found no matching records | Valid empty result — `isError: false` | No retry — this IS the answer |

---

## Best Practices

- Subagents perform **local retry** for transient failures before escalating
- Only propagate errors that cannot be locally resolved
- Synthesis outputs should include **coverage annotations** noting well-supported vs. gap areas
- Structured error context enables coordinators to choose intelligent recovery strategies

---

## Key Exam Points

- All 4 error context elements are required in propagated errors
- Silent suppression is **worse** than propagating an error
- Pipeline termination on partial failure loses valuable completed work
- Local retry first, escalate second
- `isError: false` with empty results = do not retry

---

## Common Exam Trap

> "A web search subagent times out. It returns `{ results: [], isError: false }`. The coordinator does nothing and reports no findings. What went wrong?"

Answer: The subagent used **silent suppression** — returned an empty success instead of an error. The coordinator had no information to trigger recovery. Fix: return `isError: true` with failure type, attempted action, and partial results.
