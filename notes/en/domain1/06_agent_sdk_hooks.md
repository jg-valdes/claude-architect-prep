# Agent SDK Hooks

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## What Are SDK Hooks?

Agent SDK hooks inject **deterministic behavior** into the model's probabilistic decision-making by intercepting tool calls and results to enforce business rules and normalize data.

---

## Two Hook Types

### PostToolUse Hooks (After Execution)
- Run **after** a tool executes, before the model processes results
- Transform and normalize tool outputs into consistent formats
- **Cannot block** actions — the tool already ran

**Use cases:**
- Converting Unix timestamps to ISO 8601
- Converting numeric codes to readable strings
- Normalizing heterogeneous tool output formats

### Tool Call Interception Hooks (Before Execution)
- Run **before** a tool executes
- Can block, modify, or redirect outgoing tool calls
- Prevent non-compliant actions from occurring entirely

**Use cases:**
- Refund threshold validation (block amounts >$500)
- Compliance prerequisite gates (AML checks before transfers)
- Manager approval workflows for high-value operations

---

## Decision Framework

| Requirement | Mechanism | Guarantee |
|---|---|---|
| Must follow 100% of the time | Hooks (pre-execution) | Deterministic |
| Occasional deviation acceptable | Prompts | Probabilistic |
| Data normalization after tool | Hooks (post-execution) | Deterministic |

**Critical rule:** Use hooks when a single failure causes financial loss or legal risk. Use prompts for formatting preferences and style guidelines.

---

## Lifecycle Hooks for Subagents

The Claude Agent SDK provides:
- **SubagentStart** — fires when subagents are spawned; useful for rate limiting and validation
- **SubagentStop** — fires upon subagent completion; enables output validation and sensitive data removal

Subagents can define scoped PreToolUse/PostToolUse hooks affecting only their execution.

---

## Key Exam Points

- PostToolUse hooks **cannot block** actions — they run after execution
- Pre-execution (interception) hooks are the **only** way to guarantee 100% compliance
- Prompt-based instructions cannot guarantee compliance for regulatory requirements
- PostToolUse = data transformation; Pre-execution = policy enforcement
- SDK also provides SubagentStart/SubagentStop hooks

---

## Common Exam Trap

> "You need to guarantee that AML checks complete before fund transfers 100% of the time. What's the correct approach?"

Answer: **Tool call interception hook** (pre-execution) that blocks `transfer_funds` from executing until `aml_check` returns a verified pass result.

Wrong answers:
- Prompt instructions (probabilistic, ~90-95%)
- PostToolUse hooks (run after — can't block)
