# Session State and Resumption

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## Three Session Management Options

### Option 1: `--resume <session-name>`

Restores a named session with its complete conversation history, including all tool results and analyses.

**Use when:** Prior context is mostly still valid and files remain unchanged.

**Avoid when:** Code has been modified — creates the **stale context problem**.

---

### Option 2: `fork_session`

Creates **independent branches** from a shared analysis baseline. Each branch operates independently — changes in one do not affect the other.

**Use when:** Exploring divergent approaches from a shared starting point (e.g., comparing two refactoring strategies).

---

### Option 3: Fresh Start with Summary Injection

Launches a completely new session while injecting a structured summary of prior findings.

**Use when:**
- Tool results from the prior session are stale (files changed, APIs updated, dependencies shifted)
- The prior session has grown cluttered with accumulated context

**Advantage:** Eliminates stale tool results while preserving valuable knowledge.

---

## Decision Matrix

| Scenario | Best Approach | Reason |
|---|---|---|
| Continuing work, no changes | `--resume` | Valid context, useful history |
| Comparing approaches | `fork_session` | Divergent exploration |
| Modified files | Fresh start + summary | Avoids stale context |
| Long cluttered session | Fresh start + summary | Clean baseline needed |

---

## The Stale Context Problem

When resuming after file modifications, the agent reasons from **cached tool results showing old code** while accessing new file versions simultaneously — producing contradictory guidance.

**Simply asking the agent to re-read modified files is insufficient** because stale tool results remain in the conversation history.

**Reliable solution:** Fresh session with targeted re-analysis limited to changed files only.

---

## Key Exam Points

- `--resume` preserves full history — **avoid after code modifications**
- `fork_session` = divergent branches from a shared baseline
- Fresh start + summary = best for stale tool results
- Re-reading files within a resumed session does **not** fix stale history
- `fork_session` inherits stale session data — do NOT use it to fix staleness

---

## Common Exam Trap

> "After modifying 3 of 50 files in a session, the agent gives contradictory advice. What's the best approach?"

Answer: Start fresh with an injected summary while specifying the three changed files for targeted re-analysis. Avoids wasteful full re-exploration while eliminating stale context.

Wrong answers:
- Resume and ask the agent to re-read modified files (stale tool results remain in history)
- `fork_session` (inherits the stale session's data)
- Full codebase re-exploration (wasteful when only 3 files changed)
