# Subagent Coordination

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## The Agent Tool

The **Agent tool** (previously called "Task") is the API mechanism for spawning subagents. The old "Task" name remains functional as a backward-compatible alias.

**Hard requirement:** A coordinator's `allowedTools` must explicitly include `"Agent"` or `"Task"` to spawn subagents. Without it, the coordinator cannot invoke any subagent, regardless of other configuration.

Each subagent is defined by an **AgentDefinition** specifying:
1. Description (what the subagent does)
2. System prompt (its instructions)
3. Tool restrictions (what tools it can access)

---

## Three Context Passing Rules

**Rule 1: Pass complete findings in full.**
Downstream agents cannot retroactively access previous results — all necessary information must be included in their prompt.

**Rule 2: Use structured formats separating content from metadata.**
Findings should include both claims and source information (URLs, document names, page numbers). Without metadata, downstream agents cannot attribute claims to sources.

**Rule 3: Design coordinator prompts around goals, not procedures.**
Goal-oriented prompts enable adaptability; procedural instructions constrain subagent flexibility.

---

## Structured Metadata Format

```json
{
  "findings": [
    {
      "claim": "Solar panel efficiency increased 25% last decade",
      "source_url": "https://example.com/solar-report",
      "document_name": "Annual Solar Industry Report 2024",
      "page_number": 14,
      "confidence": "high",
      "retrieved_by": "web_search_agent"
    }
  ]
}
```

Each finding carries **complete source attribution metadata**.

---

## Parallel Spawning

When multiple subagents perform independent tasks, emit **multiple Agent tool calls in a single coordinator response** rather than invoking sequentially across separate turns.

- Reduces latency for independent work
- The exam specifically tests latency awareness — look for answers mentioning "simultaneously" or "in a single response"

---

## `fork_session` vs. `--resume`

| Mechanism | Purpose |
|---|---|
| `fork_session` | Creates independent branches from a shared analysis baseline for **divergent exploration** |
| `--resume` | Continues a specific named session on the **same investigation path** |

These serve different purposes and are **frequently confused on exams**.

---

## Key Exam Points

- `allowedTools` must include `"Agent"` or `"Task"` — hard binary gate
- Subagents have **isolated context** — only receive explicitly passed information
- Missing citations = coordinator passed content **without structured metadata**
- Independent tasks should spawn **in parallel** (single response), not sequentially
- `fork_session` ≠ `--resume` — know the difference

---

## Common Exam Trap

> A synthesis agent produces unsourced claims while web search and document analysis subagents work correctly. What's the root cause?

**Answer:** The coordinator passes content to the synthesis agent without structured metadata — source URLs, document names, and page numbers are not included. The synthesis agent cannot cite sources it never received.
