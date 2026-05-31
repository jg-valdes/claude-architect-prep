# Iterative Refinement Techniques

**Exam weight:** Part of Domain 3 – Claude Code Configuration & Workflows (20%)

---

## Three Technique Hierarchy

Choose the technique based on the **specific problem**, not complexity:

### 1. Concrete Examples (for inconsistent interpretation)

**Trigger:** Prose descriptions produce varying results across invocations.

**Fix:** Provide 2–3 specific input/output pairs.

> "The fix is not more prose. The fix is concrete examples."

The model generalizes patterns from examples more reliably than from descriptive text. Include the *why* behind each example, not just input/output pairs.

---

### 2. Test-Driven Iteration (for complex transformations)

**Trigger:** The transformation is well-understood but complex.

**Fix:** Write tests covering happy paths, edge cases, and performance requirements. Share test failures with specific messages ("Expected X, got Y") — eliminates interpretation ambiguity.

---

### 3. Interview Pattern (for unfamiliar domains)

**Trigger:** Developer lacks domain expertise and might miss important considerations.

**Fix:** Have Claude ask clarifying questions *before* implementing. Surfaces considerations like cache invalidation strategies or consistency requirements.

---

## Feedback Delivery Strategy

| Feedback Type | Approach |
|---|---|
| Interdependent issues | Batch in one message — model sees all constraints simultaneously |
| Independent issues | Sequential — fix one at a time to avoid confusion |

---

## Key Distinction

> **Interview pattern** = unfamiliar domain where developer might miss important considerations.
>
> **Concrete examples** = developer knows the exact transformation but the model interprets it inconsistently.

---

## Key Exam Points

- More precise prose **does not** fix consistency problems — use concrete examples
- Examples must include *why* (teaches generalization, not literal pattern-matching)
- Interview pattern is for **developer knowledge gaps**, not model limitations
- Batch feedback for interdependent issues; sequential for independent ones

---

## Common Exam Traps

- Selecting "more detailed instructions" as the fix when the problem is inconsistent output
- Confusing the interview pattern with example-based techniques
- Providing input/output examples without reasoning (model can't generalize)
- Batching all feedback regardless of whether issues are interdependent
