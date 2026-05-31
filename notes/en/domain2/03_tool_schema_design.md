# Tool Schema Design

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## Why Descriptions Are Critical

**Tool descriptions are the primary mechanism LLMs use for tool selection.**

They are not optional metadata — they directly determine whether models route queries correctly. A vague description like "Retrieves customer information" creates ambiguity that leads to misrouting.

---

## Five Essential Elements

Production-grade tool descriptions must include:

1. **Primary purpose** — unambiguous statement of what the tool does
2. **Input specifications** — data types, formats, constraints, required vs. optional fields
3. **Concrete use cases** — example queries showing the tool's intended scope
4. **Limitations and edge cases** — explicit boundaries and failure modes
5. **Tool differentiation** — clear statements about when to use THIS tool vs. similar alternatives

---

## The Misrouting Problem

Minimal descriptions create ambiguity when multiple tools have overlapping purposes.

### Problem-Solving Hierarchy (low → high effort)

1. ✅ **Expand descriptions** (correct first step)
2. ✗ Few-shot examples (adds overhead without fixing root cause)
3. ✗ Routing classifiers (over-engineered)
4. ✗ Tool consolidation (valid long-term but premature)

**Always improve descriptions before attempting infrastructure changes.**

---

## Tool Splitting Strategy

Generic tools should split into purpose-specific ones:

```
analyze_document  →  extract_document_data
                     summarize_document
                     verify_document_claims
```

Each focused tool gets a clear description that prevents misrouting.

---

## Schema Design Best Practices

- Make fields **optional/nullable** when source documents may lack information — prevents hallucination by allowing honest `null` responses
- Use **enum values** like `"unclear"` for ambiguous cases
- Provide an `"other"` + detail string for edge cases
- Include format normalization instructions in prompts

---

## Exam Tip: Check System Prompt Conflicts

System prompts can contain keywords that override well-written tool descriptions. Always audit system prompts for conflicts when tool selection is unreliable.

---

## Key Exam Points

- Tool descriptions = **primary** selection mechanism, not optional metadata
- **Improve descriptions first** before adding routing infrastructure
- Tool splitting: one tool, one clear responsibility
- Optional/nullable fields prevent hallucination on missing data
- System prompt conflicts can override good descriptions — check both

---

## Common Exam Traps

- Choosing few-shot examples or routing classifiers as the first fix for misrouting
- Assuming well-written descriptions are sufficient without checking system prompt conflicts
- Keeping generic tools and expecting descriptions to handle all cases
- Making all schema fields required, which pressures the model to hallucinate values
