# Context Window Management

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## The Four Core Patterns

### 1. The Progressive Summarisation Trap

Summarizing long conversations **destroys critical transactional data**.

**Example:** "Customer wants refund of $247.83 for order #8891 placed March 3rd" → "Customer wants a refund for a recent order" after summarization.

**Solution: Persistent Case Facts Block**
Extract transactional facts into a structured block included in **every prompt but never summarized**:

```json
{
  "case_facts": {
    "customer_id": "C-88291",
    "order_id": "ORD-8891",
    "refund_amount": 247.83,
    "order_date": "2024-03-03",
    "status": "pending_review"
  }
}
```

> The persistent case facts block is the **single most important pattern** in context management.

---

### 2. The "Lost in the Middle" Effect

Models process information at document **beginnings and ends** reliably. Middle-buried findings receive less attention.

This is structural, not a prompt-engineering issue.

**Solution:** Place key findings summaries **at the start** of aggregated inputs with explicit section headers. Detailed results below the summary.

---

### 3. Tool Result Trimming

Order lookups returning 40+ fields (audit timestamps, warehouse codes, carrier IDs) consume tokens across every subsequent turn when only 5 fields are needed.

**Solution:** Trim tool results to relevant fields **before** they enter conversation history.

```python
def trim_order_result(raw):
    return {
        "order_id": raw["order_id"],
        "status": raw["status"],
        "amount": raw["amount"],
        "customer_name": raw["customer_name"],
        "created_at": raw["created_at"]
    }
```

Reduces token bloat by 80–90%.

---

### 4. Full Conversation History

The Claude API is **stateless** — each request requires complete conversation history for coherence. No server-side session exists.

**Do not** selectively truncate history — it breaks conversational understanding.

**Do:** Use case facts blocks and summarization for efficiency while maintaining full history.

---

## Key Exam Points

- Persistent case facts blocks = **never summarized**, always included in every prompt
- "Lost in the middle" = structural attention problem, not fixable by prompts
- Tool result trimming reduces context bloat by 80–90%
- Full history is required — don't truncate selectively

---

## Common Exam Traps

- Assuming progressive summarization is safe for transactional data (it destroys precision)
- Believing prompt reminders solve the lost-in-the-middle effect (it's structural)
- Keeping untrimmed tool results "in case they're needed later"
- Attempting selective conversation history truncation
