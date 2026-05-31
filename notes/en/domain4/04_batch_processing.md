# Batch Processing & Prompt Optimisation

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## Message Batches API

**Cost savings:** 50% compared to synchronous API calls.

**Limitation:** Up to **24-hour processing window** with no guaranteed latency SLA.

**Critical limitation:** Cannot support multi-turn tool calling within a single request.

---

## Decision Rule

> Keep blocking workflows synchronous. Use batch only for latency-tolerant workflows.

| Workflow Type | API | Why |
|---|---|---|
| Pre-merge checks (developers wait) | Synchronous | Blocking — users need results now |
| Real-time code reviews | Synchronous | Blocking |
| Overnight reports | Batch | Latency-tolerant |
| Weekly audit runs | Batch | Latency-tolerant |
| Agentic workflows with tool calls | Synchronous | Batch can't support tool calling |

**The exam presents a manager proposing to switch everything to batch for cost savings. The correct answer keeps blocking workflows synchronous.**

---

## SLA Calculation

Working backwards from deadlines:

**Example:** 30-hour SLA
- 24-hour maximum batch processing
- = 6 hours buffer
- → Submit batches every 4–6 hours to guarantee completion

---

## Failure Handling (Three-Step Pattern)

1. Identify failures via unique `custom_id` fields
2. Resubmit only failures with modifications (increased tokens, document chunking)
3. Pre-refine prompts on 5–10 representative documents before full batch submission

---

## Cost Optimization

Pre-submission prompt refinement dramatically reduces retry costs.

| First-pass success rate | Relative resubmission cost |
|---|---|
| 90% | Low |
| 60% | ~4x higher |

Test on a small sample set before running the full batch.

---

## Key Exam Points

- Batch API = 50% cheaper, up to 24h latency, **no tool calling**
- Blocking workflows (pre-merge) must use **synchronous** API
- `custom_id` field enables partial failure resubmission
- Pre-test prompt quality on 5–10 samples before full batch runs
- Calculate SLA headroom: deadline minus 24h = latest submission time

---

## Common Exam Traps

- Proposing batch API for blocking pre-merge workflows (wrong — developers are waiting)
- Forgetting that batch API cannot support tool calling
- Ignoring the `custom_id` mechanism for partial retries
- Skipping sample refinement before large batch runs
