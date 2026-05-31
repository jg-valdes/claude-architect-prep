# Agentic Loops

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## What Is an Agentic Loop?

An agentic loop is the **core execution cycle** that powers every Claude-based agent. It repeats a deterministic four-step pattern until completion.

---

## The Four-Step Lifecycle

| Step | Action |
|------|--------|
| 1. Send Request | Call the Messages API with conversation history + prior tool results |
| 2. Inspect `stop_reason` | Check the response field that signals what happens next |
| 3. Handle `tool_use` | Execute requested tools, append results to conversation history |
| 4. Handle `end_turn` | Present final response when Claude signals completion |

---

## The `stop_reason` Field — The Only Reliable Signal

`stop_reason` is **the only reliable signal for loop control**. Two values matter:

- `"tool_use"` → continue looping, execute tools
- `"end_turn"` → terminate the loop, return response

### Three Anti-Patterns to Avoid

| Anti-Pattern | Why It Fails |
|---|---|
| Parsing natural language ("I'm done") | Ambiguous — Claude may say similar phrases mid-task |
| Arbitrary iteration caps as primary stop | Wastes resources, may terminate valid work early |
| Checking `response.content[0].type == "text"` | Fails — Claude returns text *alongside* `tool_use` blocks simultaneously |

---

## Model-Driven vs. Pre-Configured

Claude **decides tool selection based on context** (model-driven). Pre-configured decision trees lack this adaptability.

**Exception:** deterministic compliance requirements override model flexibility — use programmatic enforcement there.

---

## Safety Iteration Caps

Acceptable **as fallback safeguards** against runaway loops — never as the primary termination mechanism.

---

## Minimal Footprint Principle

Core Anthropic design principle:

> Request only necessary permissions. Take only necessary actions. Prefer reversible over irreversible. Do less and confirm when uncertain.

In practice:
- Don't `rm -rf` when you can move to a temp directory
- Don't process all records when you can process one and verify first
- Ask for clarification before irreversible actions in ambiguous situations

---

## Key Exam Points

- `stop_reason` is the **only** reliable loop-control signal
- **Never** use content-type checking or natural language parsing as stop conditions
- Safety iteration caps are **fallbacks**, not primary mechanisms
- Model-driven tool selection enables flexibility; programmatic enforcement provides guarantees
- An agent that can't make progress should **escalate to a human**, not loop forever

---

## Common Trap on the Exam

> A customer support agent checked `response.content[0].type == "text"` for completion, missing explanatory text paired with `tool_use` blocks.

Answer: Replace content checks with `stop_reason` verification. Claude can return text and `tool_use` simultaneously.
