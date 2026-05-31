# Reliability Patterns for Agentic Systems

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## Workflow Enforcement: Programmatic vs. Prompt-Based

| Approach | Reliability | When to Use |
|---|---|---|
| Prompt-based guidance | ~90–95% (probabilistic) | Low-stakes operations, formatting preferences |
| Programmatic enforcement | 100% (deterministic) | Financial operations, security controls, compliance |

**The decision rule:** If a single failure would cause financial loss, security breach, or compliance violation → use programmatic enforcement.

---

## Prerequisite Gates

Code-level blocks that prevent tool execution until preconditions are satisfied.

**Example:** `process_refund` cannot execute until `get_customer` has returned verified credentials in the current session.

This eliminates the documented ~8% failure rate from prompt-only approaches.

---

## Human-in-the-Loop Triggers

Valid triggers for escalating to a human:

1. **Explicit human request** — customer directly asks to speak with a person; honor immediately without attempting resolution first
2. **Policy exceptions or gaps** — situation falls outside documented policy boundaries
3. **Inability to progress** — tool errors, insufficient system access, or technical issues requiring engineering intervention

**Anti-patterns (unreliable triggers):**
- Sentiment-based escalation — frustration level doesn't correlate with case complexity
- Self-reported confidence scores — poorly calibrated, models show false confidence on hard cases

---

## Structured Handoff Requirements

Human agents lack conversation transcript access, so handoffs must be **self-contained** with five mandatory fields:
1. Customer ID
2. Conversation summary
3. Root cause analysis
4. Refund amount (if applicable)
5. Recommended action

---

## Checkpointing for Long Tasks

For extended agentic operations:
- Save intermediate results to persistent storage at key milestones
- On failure, resume from last checkpoint rather than restarting from scratch
- Include session state: current phase, explored paths, key findings, pending steps

---

## Graceful Degradation

When a subagent fails:
- **Don't silently suppress** — return empty results marked as success; this prevents all recovery
- **Don't terminate the pipeline** — discard work from successfully-completed subagents
- **Do propagate structured errors** with: failure type, attempted action, partial results, alternative approaches

---

## Key Exam Points

- Programmatic enforcement = **deterministic**; prompts = **probabilistic**
- Use prerequisite gates for financial/security/compliance workflows
- Frustrated customers with resolvable issues → resolve, don't escalate
- Calm customers requesting policy exceptions → escalate, don't attempt resolution
- Handoffs must be self-contained — humans don't have transcript access
- Silent error suppression and pipeline termination are both **wrong**

---

## Common Exam Trap

> "A compliance requirement mandates that AML checks must complete before any fund transfer. What's the correct enforcement mechanism?"

Answer: A **tool call interception hook** (pre-execution) that blocks `transfer_funds` until `aml_check` returns verified. Prompt instructions cannot guarantee 100% compliance — only programmatic enforcement can.
