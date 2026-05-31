# Escalation & Ambiguity Resolution

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## Three Valid Escalation Triggers

Exactly three situations warrant escalating to a human:

1. **Explicit human request** — customer directly asks to speak with a person; honor immediately without attempting resolution first
2. **Policy exceptions or gaps** — situation falls outside documented policy boundaries; requires human judgment
3. **Inability to progress** — tool errors, insufficient system access, or technical issues requiring engineering intervention

---

## Two Unreliable Escalation Triggers (Anti-Patterns)

| Anti-Pattern | Why It Fails |
|---|---|
| Sentiment-based escalation | Frustration level doesn't correlate with complexity — an angry customer may have a simple resolvable issue |
| Self-reported confidence scores | LLMs show false confidence on difficult cases while being unnecessarily uncertain on easy ones |

---

## Key Nuance: Frustrated Customers

- Frustrated customer + **resolvable issue** → acknowledge frustration, provide resolution (do NOT escalate)
- Frustrated customer + **policy exception needed** → escalate (trigger #2)
- Customer reiterates preference for human after being offered help → escalate (trigger #1)

---

## Ambiguous Customer Matching

When multiple customer records match a search query:
- **Request disambiguating information** (email, phone, order number)
- **Never select heuristically** — prevents privacy violations and incorrect actions

---

## Recommended Solution for Poor Escalation

Add **explicit escalation criteria with few-shot examples** directly to system prompts — a proportionate approach before infrastructure changes like classifier models or sentiment analysis.

---

## Key Exam Points

- Exactly **3 valid escalation triggers** — memorize them
- Sentiment ≠ complexity — don't use frustration as an escalation signal
- LLM confidence scores are poorly calibrated — don't use as primary trigger
- Ambiguous customer match → always disambiguate, never guess
- Fix poor escalation with explicit criteria + few-shot examples first

---

## Common Exam Trap

> "An agent escalates every time a customer expresses frustration. What's the root cause?"

Answer: Sentiment-based escalation is unreliable. Fix: add explicit categorical escalation criteria with few-shot examples distinguishing resolvable frustrated cases from genuine escalation triggers.
