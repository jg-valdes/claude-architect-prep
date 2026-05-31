# Human Review & Confidence Calibration

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## The Aggregate Metrics Trap

> "A system reports 97% overall accuracy. The team celebrates."

This masks catastrophic failures on specific document types. A 97% aggregate can hide 40% error rates on handwritten receipts or international formats because standard invoices dominate volume.

**Rule:** Validate accuracy by **document type AND field segment separately** — never rely on aggregate metrics alone for automation decisions.

---

## Confidence Calibration

Raw model confidence scores lack absolute meaning. A 0.90 confidence score might indicate:
- 94% actual accuracy on dates
- 82% actual accuracy on amounts

**Calibration requires labeled validation sets** — real examples where correct answers are known. Build calibration curves mapping reported confidence to actual accuracy.

---

## Stratified Random Sampling

Must include **high-confidence extractions**, not just low-confidence items.

Why: Detects novel error patterns that didn't exist in validation sets and confirms ongoing accuracy maintenance across segments.

---

## Reviewer Prioritization

Route **highest-uncertainty items first** rather than distributing capacity evenly. Use dynamic queue ordering so reviewers always address the most uncertain extractions next.

---

## Implementation Sequence

1. Measure accuracy by document type and field
2. Calibrate confidence using labeled validation sets
3. Set calibrated thresholds for automation routing
4. Implement stratified random sampling
5. Only then reduce human review on validated segments

---

## Key Exam Points

- Aggregate accuracy can hide segment-level failures — always segment validation
- Raw confidence scores are uncalibrated — require labeled validation sets
- Sampling must include **high-confidence items** (to detect novel patterns)
- Route highest-uncertainty items first
- Reduce human review **only after** calibration is complete

---

## Common Exam Traps

- Using aggregate accuracy to justify full automation
- Sampling only low-confidence extractions (misses novel error patterns in high-confidence)
- Deploying uncalibrated raw confidence scores for routing decisions
- Distributing reviewer capacity evenly across all extractions instead of prioritizing uncertain ones
