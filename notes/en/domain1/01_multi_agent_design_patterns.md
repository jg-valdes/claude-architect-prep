# Multi-Agent System Design Patterns

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## Why Multi-Agent Systems?

A single Claude instance has limits: one context window, one thread of execution, one set of tools. Multi-agent systems solve this by splitting complex work across specialized agents that run in parallel or in sequence.

**Use multi-agent when:**
- The task is too large for one context window
- Subtasks can run in parallel to save time
- Different subtasks need different tools or specializations
- You need independent verification (one agent checks another's work)

---

## The Two Core Roles

### Orchestrator
- Breaks down the overall goal into subtasks
- Decides which subagent handles what
- Collects and synthesizes results
- Handles errors and retries
- **Does not do the actual work** — it directs

### Subagent
- Receives a specific, bounded task
- Executes it using its tools
- Returns a result
- Has **no memory of previous turns** — each invocation is stateless
- Should not need to know about other subagents

---

## Key Design Patterns

### 1. Hub-and-Spoke (most common)
One orchestrator, many specialized subagents. The orchestrator is the hub; subagents are the spokes.

```
Orchestrator
├── Research Agent   → searches the web
├── Writer Agent     → drafts content
├── Review Agent     → checks the draft
└── Formatter Agent  → outputs final format
```

Best for: workflows with distinct, sequential or parallel phases.

### 2. Pipeline (sequential)
Output of one agent feeds into the next. No central orchestrator — each agent passes work forward.

```
Ingestion Agent → Processing Agent → Validation Agent → Output Agent
```

Best for: data transformation, ETL-style tasks where each step transforms the output.

### 3. Parallel Fan-Out
Orchestrator sends the same task to multiple agents simultaneously, then aggregates results.

```
Orchestrator
├── Agent A (analyzes from angle 1) ─┐
├── Agent B (analyzes from angle 2) ─┼→ Orchestrator aggregates
└── Agent C (analyzes from angle 3) ─┘
```

Best for: research tasks, getting multiple independent perspectives, speed.

### 4. Hierarchical (nested orchestration)
An orchestrator delegates to sub-orchestrators, which in turn manage their own subagents.

```
Top Orchestrator
├── Sub-Orchestrator A
│   ├── Agent A1
│   └── Agent A2
└── Sub-Orchestrator B
    ├── Agent B1
    └── Agent B2
```

Best for: very large, complex tasks with multiple distinct workstreams.

---

## Key Exam Points

- **Subagents are stateless** — they don't remember prior interactions; the orchestrator must pass all needed context in each call
- **Orchestrators synthesize, subagents execute** — never conflate the two roles
- Multi-agent is not always better — for simple tasks, one agent is faster and cheaper
- **Parallelism is a key benefit** — agents that don't depend on each other should run simultaneously
- Independent verification (one agent checks another's output) improves reliability

---

## Common Trap on the Exam

> "An orchestrator needs to pass context from a previous subagent's result to the next subagent. How does this work?"

Answer: The **orchestrator** holds the results and explicitly includes the relevant context in the prompt it sends to the next subagent. Subagents do not share memory — the orchestrator is the single source of state.
