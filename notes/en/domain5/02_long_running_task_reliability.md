# Long-Running Task Reliability

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## The Problem with Long Tasks

Short tasks fail rarely and recover easily. Long-running agentic tasks face compounding risks:
- Network timeouts mid-task
- Context window filling up
- Errors in step 3 that corrupt steps 4–20
- The model drifting from the original goal
- Resource limits (rate limits, token budgets)

Good design anticipates these failures and handles them gracefully.

---

## Pattern 1: Checkpointing

Save state after each significant step so you can resume without starting over.

```
Task: Process 1,000 records

Step 1: Process records 1–100   → save progress: {"last_processed": 100}
Step 2: Process records 101–200 → save progress: {"last_processed": 200}
...
Step 6: Process records 501–600 → FAILURE

Resume: read checkpoint → last_processed = 500 → start from 501
```

**What to checkpoint:**
- Which items have been processed
- Intermediate results that took effort to produce
- Current phase/stage of the workflow

**Where to store checkpoints:**
- Files (simple, durable)
- Databases (queryable, structured)
- Key-value stores (fast, lightweight)

---

## Pattern 2: Idempotent Operations

Design each step so that running it twice produces the same result as running it once.

```
Bad (not idempotent):
  "Append row to table" → running twice creates duplicate rows

Good (idempotent):
  "Upsert row by ID" → running twice produces the same single row
```

Idempotent operations make retries safe — you can always retry without worrying about side effects.

---

## Pattern 3: Progress Tracking

Maintain a visible record of what's been done, what's in progress, and what's remaining.

```json
{
  "total": 1000,
  "completed": 547,
  "in_progress": 12,
  "failed": 3,
  "failed_items": [42, 156, 389],
  "remaining": 438
}
```

Progress tracking enables:
- Resuming from partial completion
- Identifying patterns in failures
- Estimating time to completion
- Reporting status to humans

---

## Pattern 4: Graceful Degradation

When a subtask fails, decide whether the overall task can continue without it.

```
Critical path:     Must succeed — abort if it fails
Optional step:     Can skip — continue with reduced output
Best-effort step:  Try 3 times — if all fail, note it and continue
```

Not every failure should abort the entire task. Design explicit policies per step type.

---

## Pattern 5: Timeouts and Deadlines

Always set timeouts on individual operations and overall task duration.

```
Per-tool-call timeout:  30 seconds
Per-subagent timeout:   5 minutes
Overall task timeout:   2 hours → escalate to human if exceeded
```

Without timeouts, a single hung operation blocks the entire workflow indefinitely.

---

## Handling Context Growth in Long Tasks

As a long task progresses, the context window fills with history. Strategies:

1. **Periodic summarization** — every N steps, summarize what's been done and replace the raw history
2. **Rolling window** — keep only the last N steps in context; older steps are checkpointed externally
3. **Fresh subagents** — for each major phase, spawn a new subagent with only the context it needs (avoids context accumulation entirely)

---

## Key Exam Points

- **Checkpointing** is mandatory for long tasks — design it from the start, not as an afterthought
- **Idempotent operations** make retries safe — always prefer them for steps that may be repeated
- **Timeouts** are required at every level — per-operation, per-phase, and overall
- **Graceful degradation** — classify each step as critical, optional, or best-effort upfront
- **Context growth** in long tasks must be managed via summarization, rolling windows, or fresh subagents

---

## Common Trap on the Exam

> "A long-running agentic task processes 1,000 records and fails on record 750. Without any reliability patterns in place, what is the consequence?"

Answer: The entire task must **restart from the beginning** — all 750 completed records are lost. The correct design is checkpointing after each batch so the task resumes from record 750, not record 1.
