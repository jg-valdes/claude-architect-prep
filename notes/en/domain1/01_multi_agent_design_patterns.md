# Multi-Agent Design Patterns

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## The Hub-and-Spoke Pattern

The exam focuses on one specific model: **hub-and-spoke with a central coordinator**. This is the tested pattern.

```
         ┌──────────────┐
         │  Coordinator │  ← central hub
         └──────┬───────┘
      ┌─────────┼─────────┐
      ▼         ▼         ▼
  [Search]  [Analyze]  [Synthesize]  ← subagents
  Agent     Agent       Agent
```

---

## Two Roles

### Coordinator Agent
- Receives the initial task
- Breaks it into pieces
- Selects which subagents to activate
- Passes context to subagents
- Collects results and manages errors
- Routes information between subagents

### Subagents
- Specialized workers handling distinct functions
- Receive instructions from the coordinator **only**
- Return results **exclusively** to the coordinator

---

## The Cardinal Rule

> **ALL communication flows through the coordinator. Subagents never communicate directly with each other.**

This applies universally — not for efficiency, not for convenience, never.

---

## Critical Isolation Principle

**Subagents lack automatic access to:**
- The coordinator's system instructions
- Previous coordinator messages
- Other subagents' outputs (unless explicitly shared)
- Shared memory or global state

**Memory doesn't persist:** Two invocations of the same subagent are completely independent. The second has no knowledge of the first.

The coordinator must deliberately include every piece of information each subagent needs in its prompt.

---

## Four Coordinator Responsibilities

1. **Dynamic Selection** — choose which subagents to invoke based on requirements; not all queries need the full pipeline
2. **Scope Partitioning** — divide topics to eliminate duplication (one agent handles academic sources, another handles news)
3. **Iterative Refinement** — evaluate output for gaps and re-delegate with targeted queries
4. **Centralized Routing** — all communication passes through the coordinator

---

## The Narrow Decomposition Failure Pattern

A coordinator assigned "impact of AI on creative industries" but only addressed visual arts, missing music, writing, and film.

- The search agent found everything assigned to it ✓
- The synthesis agent combined what it received ✓
- But nothing was ever assigned for the missing domains ✗

**Root cause: coordinator decomposition, not subagent capability.**

When output is incomplete in *scope* (not depth), trace failures to the coordinator's decomposition.

---

## Key Exam Points

- Hub-and-spoke is the tested pattern — memorize the two roles
- Subagents are **stateless** and **isolated** — no shared memory, no inherited context
- Scope gaps are always the **coordinator's fault**, not the subagent's
- Direct subagent-to-subagent communication is **never** correct
- Adding more subagents does not fix decomposition problems

---

## Common Exam Traps

- Blaming subagents for coverage gaps that originate in coordinator decomposition
- Assuming subagents inherit coordinator context or share memory
- Proposing direct inter-subagent communication as an efficiency fix
- Adding more subagents to solve decomposition problems
