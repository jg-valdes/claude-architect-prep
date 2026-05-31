# Information Provenance & Multi-Source Synthesis

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## The Core Challenge

> "Attribution dies during summarisation."

Synthesis agents compress and paraphrase without explicit instructions to preserve claim-source mappings.

---

## Five Required Elements per Claim

Each finding in a multi-source synthesis must include:

1. **Claim** — the specific assertion
2. **Source URL** — where information was found
3. **Document name** — source title
4. **Relevant excerpt** — supporting passage
5. **Publication date** — when published or data collected

---

## Conflict Handling

When sources report different statistics: **annotate both values with full attribution and publication dates** — do not arbitrarily select one.

This preserves information and lets consumers decide based on context.

---

## Temporal Awareness

Different publication dates explain different numbers:
- 2023 source: 8% growth
- 2024 source: 12% growth

This may indicate **acceleration**, not contradiction. Dates must accompany all data.

---

## Content-Appropriate Rendering

| Content Type | Rendering |
|---|---|
| Financial data | Tables |
| News items | Prose paragraphs |
| Technical findings | Structured lists |

Uniform formatting degrades comprehension across different content types.

---

## Multi-Step Pipeline Requirement

Attribution must survive each stage:

```
Research subagents → output structured mappings
        ↓
Analysis agents → preserve claim-source mappings
        ↓
Synthesis agents → merge while maintaining mappings
        ↓
Final reports → include inline citations
```

---

## Key Exam Points

- Attribution must be explicitly preserved at every pipeline stage — it doesn't survive by default
- Conflicting sources → annotate both, don't pick one
- Temporal differences explain data differences — don't call it a contradiction
- Content-appropriate formatting is required — no uniform format
- Pass structured metadata (not just content) to downstream agents

---

## Common Exam Traps

- Selecting the most recent source when conflicts exist (instead of annotating both)
- Treating different numbers from different dates as contradictions
- Allowing synthesis to paraphrase without preserving claim-source mappings
- Rendering all output in uniform format regardless of content type
