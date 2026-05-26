# Task Decomposition

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## What Is Task Decomposition?

Task decomposition is how an orchestrator breaks a large, complex goal into smaller, bounded subtasks that subagents can execute reliably.

Good decomposition is the difference between a system that works and one that drifts, hallucinates, or fails silently.

---

## Principles of Good Decomposition

### 1. Each subtask should be independently verifiable
A subagent's output should be checkable without running the whole system. If you can't tell whether a subtask succeeded, you can't catch failures.

### 2. Subtasks should have clear inputs and outputs
Vague tasks produce vague results. Define:
- What data/context goes in
- What format the result should be in
- What "done" looks like

### 3. Minimize dependencies between subtasks
Tasks that don't depend on each other can run in parallel. Map dependencies explicitly before designing the flow.

### 4. Keep subtasks within one context window
If a subtask requires more context than fits in one window, it needs to be decomposed further.

### 5. Fail fast — validate early
Put validation steps early in the pipeline. Don't run expensive subagents on bad input.

---

## How to Decompose: A Framework

Given a complex task, ask:

1. **What are the distinct phases?** (research, write, review, format)
2. **Which phases depend on each other?** (write depends on research; review depends on write)
3. **Which phases can run in parallel?** (multiple research threads)
4. **What does each phase need as input?** (specific data, not "everything")
5. **What does each phase produce?** (a specific artifact)
6. **How do we verify each phase succeeded?** (schema check, human review, automated test)

---

## Example: Research Report System

**Bad decomposition:**
```
Subagent 1: "Research the topic and write a report"
```
Too large, not verifiable, can't parallelize.

**Good decomposition:**
```
Subagent 1a: "Search for academic sources on X" → list of URLs + summaries
Subagent 1b: "Search for news sources on X"     → list of URLs + summaries
Subagent 1c: "Search for data/statistics on X"  → list of URLs + summaries
  (all three run in parallel)

Subagent 2: "Given these sources, extract key findings" → structured JSON

Subagent 3: "Given these findings, write a 500-word summary" → markdown

Subagent 4: "Review the summary for accuracy against the sources" → approval or corrections
```

---

## Checkpointing

For long multi-step workflows, save intermediate results so failures don't require starting over:

- After each major subagent completes, persist its output
- On failure, resume from the last successful checkpoint
- Never assume a long agentic task will complete without interruption

---

## Key Exam Points

- **Decompose until each subtask fits in one context window** — that's the practical lower bound
- **Parallel execution requires independent subtasks** — if A needs B's output, they're sequential
- **Define success criteria per subtask** before running — not after
- **Checkpointing** is essential for reliability in long workflows
- The orchestrator owns decomposition logic — subagents just execute

---

## Common Trap on the Exam

> "Which task decomposition approach is most appropriate for a complex research task that requires gathering data from multiple sources simultaneously?"

Answer: **Parallel fan-out** — spawn multiple subagents simultaneously to research different sources, then have the orchestrator aggregate results. Sequential decomposition would work but is much slower with no benefit.
