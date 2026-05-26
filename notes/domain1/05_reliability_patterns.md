# Reliability Patterns for Agentic Systems

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## Why Reliability Is Hard in Agentic Systems

Agentic systems are non-deterministic, long-running, and take real-world actions. Failures compound: one bad step can corrupt all subsequent steps. Unlike traditional software, you can't always roll back.

Reliability must be designed in — it doesn't emerge naturally.

---

## Core Reliability Patterns

### 1. Define Success Criteria Upfront
Before any agent takes action, define what "done" and "correct" look like.

```
Bad:  "Fix the bug"
Good: "The bug is fixed when `npm test` passes with 0 failures and the 
       error no longer appears in the logs"
```

Without a clear definition, the agent can't know when to stop or whether it succeeded.

---

### 2. Checkpointing
Save intermediate results so long workflows can resume after failure.

```
Step 1 complete → save result to disk
Step 2 complete → save result to disk
Step 3 fails    → resume from Step 2's saved result
```

Never assume a long agentic task will complete without interruption.

---

### 3. Validation Gates
Check the output of each step before passing it to the next.

```
Subagent produces output
  → Validate format (is it valid JSON?)
  → Validate content (are required fields present?)
  → Validate correctness (does it pass a spot check?)
  → Only then pass to next subagent
```

Validation gates prevent bad data from propagating through the pipeline.

---

### 4. Retry with Backoff
For transient failures (network errors, rate limits), retry with increasing delays:

```
Attempt 1 fails → wait 1s → retry
Attempt 2 fails → wait 2s → retry
Attempt 3 fails → wait 4s → retry
Attempt 4 fails → escalate
```

Always cap retries. Infinite retries can hide real failures.

---

### 5. Human-in-the-Loop Escalation
For high-stakes or irreversible actions, pause and get human approval:

```
Agent plans to delete 10,000 records
  → Pause
  → Show human what will be deleted
  → Wait for explicit approval
  → Then proceed
```

Key triggers for human escalation:
- Irreversible actions (delete, publish, send)
- Ambiguous instructions that could be interpreted multiple ways
- Repeated failures the system can't resolve
- Actions outside the original scope

---

### 6. Minimal Footprint
Limit the blast radius of mistakes:

- Request **only the permissions** needed for the task
- Take **only the actions** required to achieve the goal
- Prefer **reversible actions** (move vs delete, staging vs prod)
- Take **small steps** and validate before proceeding

---

### 7. Independent Verification
Have a separate agent review the primary agent's work:

```
Writer Agent produces output
  → Reviewer Agent checks it independently
  → Discrepancies flagged to orchestrator
```

Especially useful for high-stakes outputs (code going to production, financial calculations, etc.).

---

## Reliability Anti-Patterns to Avoid

| Anti-pattern | Problem |
|---|---|
| No stop conditions | Loop runs forever or overshoots goal |
| No checkpointing | Full restart required after any failure |
| Trusting subagent output without validation | Bad data propagates silently |
| Unlimited retries | Hides real failures, wastes resources |
| Taking irreversible actions without confirmation | Can't undo mistakes |
| Giving agents too many permissions | Larger blast radius when things go wrong |

---

## Key Exam Points

- **Define success criteria before starting** — not after
- **Checkpointing** is the primary defense against long-task failures
- **Validation gates** between steps prevent error propagation
- **Human escalation** is correct for irreversible or ambiguous actions — not a design flaw
- **Minimal footprint** reduces blast radius and is an explicit Anthropic design principle
- **Independent verification** is a reliability pattern, not just a quality pattern

---

## Common Trap on the Exam

> "An agentic system is about to send 50,000 emails to customers. What reliability pattern is most important to apply here?"

Answer: **Human-in-the-loop escalation** — sending emails is irreversible. The system should pause, show exactly what will be sent and to whom, and require explicit human approval before proceeding. No amount of automated validation substitutes for human sign-off on irreversible, high-impact actions.
