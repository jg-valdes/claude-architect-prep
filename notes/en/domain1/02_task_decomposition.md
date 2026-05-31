# Task Decomposition

**Exam weight:** Domain 1 – Agentic Architecture & Orchestration (27%)

---

## Two Decomposition Patterns

### Pattern 1: Fixed Sequential Pipelines (Prompt Chaining)

Work executes through **predetermined steps in order**. Each step's output feeds into the next. The sequence never changes regardless of intermediate results.

**Example:** Code review pipeline → local file analysis → cross-file integration check → compile unified report.

**Strengths:** Consistency, reliability, debuggability.

**Best for:** Predictable, structured tasks — code reviews, document processing, data extraction, compliance checks.

---

### Pattern 2: Dynamic Adaptive Decomposition

Agents start with a high-level goal, investigate, and **generate plans based on discoveries**. Plans evolve as new information emerges.

**Example:** Adding tests to legacy codebase — map structure → identify high-impact areas → create test plan → adapt as dependencies emerge.

**Strengths:** Adaptability and thoroughness for open-ended problems.

**Best for:** Open-ended tasks — legacy system exploration, security audits, debugging unfamiliar code.

---

## Selection Matrix

| Task Characteristics | Pattern | Rationale |
|---|---|---|
| Known steps, structured input | Fixed pipeline | Reliability matters more than adaptability |
| Open-ended, unknown scope | Dynamic decomposition | Adaptability essential when problem undefined |
| Multi-file code review | Fixed pipeline | Predictable: per-file + cross-file |
| Legacy exploration | Dynamic decomposition | Issues emerge during investigation |
| Document extraction | Fixed pipeline | Fields and format predetermined |
| Debugging unfamiliar systems | Dynamic decomposition | Root cause unknown |

---

## Attention Dilution Problem

**Definition:** Processing too many items in one pass produces **inconsistent analysis depth**.

**Symptoms:**
- Detailed feedback for files 1–5, increasingly shallow for files 10–14
- Same pattern flagged as inefficient in File 3, approved in File 11
- Obvious bugs missed in some files while minor issues caught elsewhere

**Root cause:** Model allocates attention across all items. When items exceed capacity, attention per item decreases.

**Solution:** Multi-pass architecture:
1. **Per-item local analysis passes** — each file analyzed individually with full attention budget
2. **Cross-file integration pass** — separate analysis checking data flow, API consistency, pattern usage

---

## Common Exam Traps

| Trap | Why It's Wrong |
|---|---|
| Suggesting a better model or larger context window | Attention dilution is architectural, not a model limitation |
| Proposing better prompts | Prompts improve average quality but don't solve fundamental attention allocation |
| Batching files without a cross-file pass | Misses cross-batch issues like data flow problems |
| Applying fixed pipelines to open-ended investigations | Wrong pattern — needs dynamic decomposition |

---

## Practice Scenario

> A code review agent produces detailed feedback for the first 5 files but misses obvious bugs in files 10–14. It flags a forEach loop as inefficient in one file while approving identical code in another.

**Correct answer:** Split into per-file local analysis passes plus a separate cross-file integration pass to avoid attention dilution. **Not** a model upgrade or context window increase.
