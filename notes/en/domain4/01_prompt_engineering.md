# Prompt Engineering

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## System Prompts with Explicit Criteria

Vague instructions fail in production because they lack actionable decision boundaries.

### Ineffective vs. Effective

| Ineffective | Effective |
|---|---|
| "Be conservative" | "Flag comments only when claimed behaviour contradicts actual code behaviour" |
| "Use best judgment" | "Report bugs and security vulnerabilities. Skip minor style preferences." |
| "Flag critical issues" | Show actual code patterns (SQL injection example) that exemplify "Critical" |

**Rule:** Replace vague adjectives with concrete categorical criteria.

---

## The False Positive Trust Problem

High false positive rates in one category erode confidence **across all categories**.

**Solution:** Temporarily disable problematic categories while refining their criteria. Do not attempt to iterate on all categories simultaneously.

---

## Severity Calibration

Concrete code examples outperform prose descriptions. Instead of defining "Critical" as "issues causing system failures," show actual code patterns that exemplify each severity level.

---

## Confidence-Based Filtering

LLM self-reported confidence is **poorly calibrated**. Use the hierarchy:
1. Explicit criteria first
2. Confidence-based routing for secondary filtering only
3. Never as a substitute for clear definitions

---

## Prompt Chaining

Break complex tasks into sequential steps where each step's output feeds the next.

**When to use:** When a single prompt would require too much reasoning in one pass, or when intermediate results need validation before proceeding.

---

## Validation-Retry Loops

### Effective Retry Requirements

All three components are required:

1. The original source document (for re-examination)
2. The failed extraction output (for comparison)
3. **Specific validation error details** — e.g., "Line items sum to £450 but stated total is £500"

Without error specificity, models reproduce identical mistakes — they lack guidance on what to correct.

---

## What Retries Can and Cannot Fix

| Fixable by retry | Not fixable by retry |
|---|---|
| Format mismatches (date formats, currency) | Information absent from source documents |
| Structural output errors | Data requiring external documents |
| Misplaced values | Fields requiring knowledge the model lacks |
| Mathematical errors (missed line items) | — |

**Unfixable errors → flag for human review**, not another retry.

---

## Self-Correction Schema Design

Embed validation signals into the extraction schema:

- `calculated_total` vs `stated_total` — extract both; divergence flags discrepancies automatically
- `conflict_detected` boolean — flags contradictory information within source documents
- `detected_pattern` fields — track which constructs triggered findings

---

## Key Exam Points

- Explicit criteria > vague adjectives — always
- Confidence thresholds don't resolve false positive problems — fix the criteria
- Retry loops require specific error details, not just "try again"
- Not all errors are retryable — know when to escalate to human
- Schema validation eliminates syntax errors but NOT semantic errors

---

## Common Exam Traps

- Selecting vague improvements ("be more precise") as valid solutions
- Assuming confidence thresholds resolve false positive problems
- Implementing retries without including specific validation errors
- Maintaining all review categories while iterating on one problematic one
