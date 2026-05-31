# Structured Output Patterns

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## Reliability Hierarchy

From most to least reliable for structured output:

1. **`tool_use` with JSON schemas** — eliminates syntax errors
2. **Prompt-based JSON** — no structural guarantees

---

## `tool_choice` Parameter Modes

| Mode | Behavior | Output Guarantee |
|---|---|---|
| `"auto"` | Model decides: tool or conversational | None — may respond with text |
| `"any"` | Model MUST call some tool, chooses which | Guaranteed structured output |
| Specific tool name | Model MUST call that exact tool | Maximum control, mandatory step |

**Critical:** `"auto"` does NOT guarantee structured output. Use `"any"` or specific tool name when structure is required.

---

## What `tool_use` With Schemas Does and Does NOT Prevent

| Prevents | Does NOT prevent |
|---|---|
| Malformed JSON | Wrong values in correct fields |
| Missing required fields | Sum discrepancies |
| Type mismatches | Misplaced data |
| — | Fabricated/hallucinated values |

> `tool_use` with JSON schemas eliminates **syntax** errors. It does NOT prevent **semantic** errors.

---

## Schema Design Best Practices

### Optional/Nullable Fields

Make fields optional or nullable when source documents may lack information:

```json
{
  "invoice_date": { "type": "string", "nullable": true },
  "tax_amount": { "type": "number", "nullable": true }
}
```

**Why:** Required fields pressure the model to invent plausible values (hallucination). Optional fields allow honest `null` responses.

### Enum Values for Ambiguity

```json
"confidence": { "enum": ["high", "medium", "low", "unclear"] }
```

Use `"unclear"` as a valid enum value instead of forcing a choice.

### Edge Case Handling

```json
"category": { "type": "string", "enum": ["billing", "technical", "other"] },
"category_detail": { "type": "string", "description": "Required when category is 'other'" }
```

---

## Multi-Instance Review Architecture

For large reviews suffering from attention dilution:

1. **Pass 1 — Per-file local analysis:** Each file analyzed individually with full attention budget
2. **Pass 2 — Cross-file integration:** Separate analysis checking data flow, API contracts, contradictions

**Why larger context windows don't help:** "A larger context window does not prevent the model from giving uneven attention across files." The problem is attention quality, not capacity.

---

## Confidence-Based Routing

Findings can include confidence scores (0.0–1.0) enabling strategic routing:
- High confidence → direct developer reporting
- Low confidence → human review queue

**Critical anti-pattern:** "Using uncalibrated confidence for automated decisions." Thresholds must be calibrated using labeled validation sets.

---

## Production Review Architecture

```
Generation → Per-file review (independent instances) → 
Integration review → Confidence routing → 
Calibration loop (labeled validation sets)
```

---

## Key Exam Points

- `"any"` guarantees a tool call; `"auto"` does not
- `tool_use` prevents syntax errors, not semantic errors
- Required fields = hallucination risk; optional/nullable = safer
- Self-review (same session) < independent instance review
- Context window size doesn't fix attention dilution
- Calibrate confidence scores before using them for routing

---

## Common Exam Traps

- Assuming `tool_use` prevents all errors (only syntax)
- Confusing `"auto"` (no guarantee) with `"any"` (guaranteed tool call)
- Making all schema fields required
- Using uncalibrated raw confidence scores for automated routing
- Recommending a larger context window to fix attention dilution
