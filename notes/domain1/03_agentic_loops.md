# Agentic Loops

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## What Is an Agentic Loop?

An agentic loop is the core execution pattern of an autonomous agent: the agent takes an action, observes the result, decides what to do next, and repeats — until the goal is achieved or a stop condition is met.

```
┌─────────────────────────────────────┐
│                                     │
│  Plan → Act → Observe → Evaluate   │
│              ↑                ↓     │
│              └── loop until done ──┘
│                                     │
└─────────────────────────────────────┘
```

---

## The Four Phases

### 1. Plan
The agent decides what action to take based on the goal and current state.
- Should reference the original goal
- Should consider what's already been done
- Should choose the minimal next action

### 2. Act
The agent calls a tool (Bash, Read, Write, WebSearch, etc.).
- One action per loop iteration is safer than batching many
- Prefer reversible actions — easier to recover from mistakes

### 3. Observe
The agent reads the tool's output.
- Did the action succeed?
- What new information is available?
- Did anything unexpected happen?

### 4. Evaluate
The agent decides: is the goal achieved? What next?
- If done → exit the loop
- If not done → go back to Plan
- If stuck or failing → escalate or abort

---

## Stop Conditions (Critical)

**Always define stop conditions before starting an agentic loop.** Without them, loops run forever, overshoot the goal, or get stuck.

Good stop conditions:
- ✅ "Stop when all tests pass"
- ✅ "Stop after processing all items in the list"
- ✅ "Stop if the same action fails 3 times in a row"
- ✅ "Stop after a maximum of N iterations"

Bad stop conditions:
- ❌ "Stop when done" (too vague — what is done?)
- ❌ No stop condition at all

**Always include a maximum iteration count** as a safety net, even if you have a real stop condition.

---

## Failure Modes to Design For

### Infinite loops
The agent keeps taking the same action that fails. Fix: track failed actions, don't retry more than N times.

### Goal drift
The agent drifts from the original goal as the loop progresses. Fix: re-read the original goal at the start of each iteration.

### Compounding errors
An early mistake causes all subsequent actions to be wrong. Fix: validate key intermediate results before continuing.

### Overly broad actions
The agent takes a large, irreversible action when a smaller one would do. Fix: prefer minimal, reversible actions at each step.

---

## Minimal Footprint Principle

This is a core Anthropic design principle for agentic systems:

> **Request only necessary permissions. Take only necessary actions. Prefer reversible over irreversible. Do less and confirm when uncertain.**

In practice:
- Don't `rm -rf` when you can move to a temp directory
- Don't write to prod when you can write to a staging environment
- Don't process all records when you can process one and verify first
- Ask for clarification before taking an irreversible action in an ambiguous situation

---

## Key Exam Points

- **Stop conditions must be defined before the loop starts** — not as an afterthought
- **Always include a max iteration count** as a safety fallback
- The **minimal footprint principle**: prefer reversible, minimal-scope actions
- **Compounding errors** are a major risk — validate intermediate results
- An agent that can't make progress should **escalate to a human**, not loop forever

---

## Common Trap on the Exam

> "An agentic loop is tasked with fixing failing tests. After 10 iterations, the tests still fail. What is the best design to handle this?"

Answer: The loop should have a **maximum iteration count** with a defined behavior on hitting it — surface the failure to the orchestrator or human operator. Continuing to loop is not an option. Silently stopping without reporting is also wrong.
