# Subagent Coordination

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## The Orchestrator–Subagent Contract

When an orchestrator spawns a subagent, it must provide everything the subagent needs — subagents have no shared memory, no access to prior conversation history, and no awareness of other subagents.

**The orchestrator is responsible for:**
- Providing complete context in each subagent call
- Collecting and storing subagent results
- Passing relevant prior results to subsequent subagents
- Handling subagent failures and deciding whether to retry, skip, or abort

**The subagent is responsible for:**
- Executing its specific task
- Returning a result in the expected format
- Reporting failure clearly if it can't complete the task

---

## Passing Context Between Subagents

Since subagents are stateless, the orchestrator must explicitly pass any context a subagent needs:

```
# Bad — subagent doesn't know what "the previous research" is
Orchestrator → Subagent B: "Write a summary of the previous research"

# Good — orchestrator passes the actual data
Orchestrator → Subagent B: "Write a summary based on this research: {research_output}"
```

**Rule:** Never reference prior work implicitly. Always include it explicitly in the prompt.

---

## Parallel vs Sequential Coordination

### Sequential (dependent tasks)
Use when task B needs task A's output:
```
Orchestrator → Subagent A → result A → Orchestrator → Subagent B(result A) → result B
```

### Parallel (independent tasks)
Use when tasks don't depend on each other:
```
Orchestrator → Subagent A ─┐
Orchestrator → Subagent B ─┼→ Orchestrator aggregates all results
Orchestrator → Subagent C ─┘
```

**Prefer parallel** wherever dependencies allow — it's faster and scales better.

---

## Result Validation

Never trust a subagent's output blindly. The orchestrator should validate:

1. **Format** — is the output in the expected structure (JSON, markdown, etc.)?
2. **Completeness** — did the subagent actually do all parts of the task?
3. **Correctness** — does the output make sense? (spot checks, schema validation)

If validation fails:
- Retry with a clearer prompt
- Try a different approach
- Escalate to a human

---

## Error Handling Strategies

| Strategy | When to use |
|---|---|
| **Retry** | Transient failures (network error, timeout) |
| **Retry with adjusted prompt** | The subagent misunderstood the task |
| **Skip and continue** | The subtask is optional; failure is acceptable |
| **Abort and report** | The subtask is critical; proceeding without it is dangerous |
| **Escalate to human** | The system can't resolve the failure automatically |

---

## Avoiding Common Coordination Mistakes

| Mistake | Fix |
|---|---|
| Giving subagents too much context | Pass only what the subagent needs for its specific task |
| Assuming subagents share state | Always pass state explicitly through the orchestrator |
| Spawning subagents for trivial tasks | Use subagents for work that justifies the overhead |
| No error handling on subagent results | Always validate and handle failures |
| Sequential tasks that could be parallel | Map dependencies; parallelize where possible |

---

## Key Exam Points

- **Subagents are completely stateless** — they know nothing except what the orchestrator tells them in this call
- **The orchestrator is the single source of truth** for system state
- **Parallel coordination** requires truly independent tasks — if there's any dependency, it must be sequential
- **Validate subagent output** before passing it to the next step
- **Error handling is the orchestrator's job** — not the subagent's

---

## Common Trap on the Exam

> "A subagent fails to complete its task due to a network timeout. What should the orchestrator do?"

Answer: **Retry** the subagent (transient failure). If retries are exhausted, **abort and report** — do not silently continue with incomplete data or infinitely retry. The exact strategy depends on whether the subtask is critical or optional.
