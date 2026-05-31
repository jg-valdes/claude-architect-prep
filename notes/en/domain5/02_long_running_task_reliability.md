# Long-Running Task Reliability & Context Degradation

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## Context Degradation (Not Token Exhaustion)

**Definition:** The model starts referencing "typical patterns" instead of the specific classes, methods, and dependency chains it discovered earlier.

**Mechanism:**
1. Each exploration step produces verbose output
2. Output accumulates in conversation context
3. Earlier discoveries get buried by recent verbose output
4. Model attention shifts, losing specific references

> **Context degradation is not a token limit problem. Increasing the context window does not fix it.**

---

## Four Mitigation Strategies

### 1. Scratchpad Files

Write key findings to persistent files; reference them for subsequent questions.

```markdown
# exploration-scratchpad.md
## Key Classes
- OrderProcessor (src/orders/processor.ts) — handles refund logic
- PaymentGateway (src/payments/gateway.ts) — wraps Stripe SDK

## Dependency Chain
OrderProcessor → PaymentGateway → StripeClient

## Critical Findings
- Missing null check on line 247 (potential NPE)
```

---

### 2. Subagent Delegation

Rather than one agent exploring everything (filling its context with verbose output), delegate specific tasks to isolated subagents:
- Find test files and coverage status
- Trace refund flows through systems
- Identify external API integrations

> "Each subagent operates with its own isolated context... the main agent's context stays clean for high-level coordination while subagents handle the verbose exploration."

**Key insight:** The primary value of subagents is **isolation**, not parallelization.

---

### 3. Summary Injection Between Phases

When exploration occurs in phases, summarize Phase 1 findings **before** spawning Phase 2 subagents. Prevents duplication and ensures architectural context carries forward.

---

### 4. The `/compact` Command

Claude Code's `/compact` command reduces context usage by summarizing conversations.

**Use proactively** during extended exploration sessions — not just when hitting context limits.

---

## Crash Recovery via State Manifests

For extended sessions that may crash or interrupt:

```json
{
  "session_id": "explore-2024-03-15",
  "current_phase": "phase_2_deep_analysis",
  "explored_paths": ["src/orders/", "src/payments/"],
  "key_findings": ["Missing null check at processor.ts:247"],
  "pending_questions": ["Does PaymentGateway retry on 429?"]
}
```

On recovery: inject the manifest as context, resume from last checkpoint.

---

## Key Exam Points

- Context degradation = attention loss from verbose accumulation, **not** token exhaustion
- Larger context windows do **not** fix context degradation
- Subagent value = **isolation** (not parallelization) for clean main context
- Use `/compact` proactively, not reactively
- Scratchpad files + state manifests enable crash recovery without full re-exploration

---

## Common Exam Traps

- Recommending context window increase for context degradation (it's attention quality, not size)
- Restarting the session without saving state (loses accumulated knowledge)
- Using `/compact` only as emergency measure rather than proactive maintenance
- Thinking subagent delegation is about speed rather than context isolation
