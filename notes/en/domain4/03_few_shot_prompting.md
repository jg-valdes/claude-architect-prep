# Few-Shot Prompting

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## Core Principle

> Few-shot examples are the **most effective technique** for achieving consistent, well-formatted output from Claude.

This takes priority over additional instructions, confidence adjustments, or temperature modifications.

---

## When to Deploy Few-Shot Examples

| Trigger | Description |
|---|---|
| Formatting inconsistency | Detailed instructions exist but produce varying structures (lists, tables, prose) |
| Ambiguous judgment calls | Model makes inconsistent classifications on borderline cases |
| Extraction gaps | Information exists in documents but in unexpected formats (narratives, embedded paragraphs) → empty/null fields |

---

## Construction Rules

### Optimal Quantity
**2–4 targeted examples.**
- Fewer than 2: don't establish patterns
- More than 4: waste tokens without proportional gains

### Include Reasoning
Examples must show **WHY** decisions were made, not just input/output pairs.

This teaches generalization to novel scenarios rather than literal pattern-matching.

### Target Failing Scenarios
If extraction works on tables but fails on narratives, examples should demonstrate **narrative extraction** specifically — not the easy cases.

---

## Technique Comparison

| Problem | Solution |
|---|---|
| Inconsistent formatting | Few-shot examples |
| Malformed JSON | `tool_use` with schemas |
| Fabricated missing values | Optional/nullable fields |
| Extraction gaps (unexpected formats) | Few-shot examples |
| Sum validation failures | Validation-retry loops |
| Ambiguous judgment calls | Few-shot examples with reasoning |

---

## Secondary Benefits

- Reduces hallucination in extraction tasks by showing how to handle structural variety
- Reduces false positives in code review by distinguishing acceptable patterns from genuine issues

---

## Key Exam Points

- Few-shot examples > more detailed instructions for consistency problems
- **2–4 examples** is the optimal count
- Always include **reasoning** in examples — not just I/O pairs
- Target the specific **failing scenario** (narratives, not easy table cases)
- Few-shot examples teach principles, not patterns

---

## Common Exam Traps

- Assuming more detailed instructions fix consistency problems (they don't)
- Treating few-shot examples as pure pattern-matching (they should include reasoning)
- Using confidence thresholds to address judgment inconsistency (use examples instead)
- Providing more than 4 examples expecting better results (diminishing returns + token waste)
