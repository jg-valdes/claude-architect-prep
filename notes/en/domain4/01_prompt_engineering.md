# Prompt Engineering & Structured Output

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## Core Prompt Engineering Principles

### 1. Be Explicit, Not Implicit
Claude does what you ask — not what you meant. State requirements directly.

```
Bad:  "Summarize this article nicely"
Good: "Summarize this article in 3 bullet points. Each bullet must be 
       under 20 words. Focus on key facts, not opinions."
```

### 2. Use XML Tags for Structure
XML tags help Claude parse complex prompts and reduce confusion between sections:

```xml
<task>
  Classify the customer complaint below into one of the categories.
</task>

<categories>
  - billing
  - technical_support
  - shipping
  - returns
</categories>

<complaint>
  My package arrived damaged and I want a refund.
</complaint>
```

### 3. Put Instructions Before Content
Claude processes prompts top-to-bottom. Instructions at the top set the frame before the content is read.

```
Good:
  "Translate the following text to Spanish. Output only the translation, 
   no explanations."
  [text to translate]

Bad:
  [text to translate]
  "Translate the above to Spanish."
```

### 4. Specify Output Format Explicitly
Never assume Claude will use the format you want. State it:

```
"Respond with a JSON object. No markdown code blocks, no explanation — 
 only the raw JSON."
```

---

## Few-Shot Prompting

Few-shot prompting gives Claude examples of the input/output pattern you want before asking it to handle the real input.

### Structure
```
[Instruction]

[Example 1 input]
[Example 1 output]

[Example 2 input]
[Example 2 output]

[Real input]
```

### Example
```
Classify each customer message as "urgent" or "normal".

Message: "My account has been hacked and I can't log in!"
Classification: urgent

Message: "Can you explain how to update my billing address?"
Classification: normal

Message: "I've been charged twice for the same order!"
Classification: urgent

Message: "Where can I find my invoice history?"
Classification:
```

### Key Rules for Few-Shot
- Examples go **before** the real input, not after
- Use **2–5 examples** — more is rarely better
- Examples should **cover edge cases**, not just easy cases
- All examples must use the **same format** you expect in the output
- Examples should be **representative** of real inputs, not cherry-picked

---

## Structured Output: JSON Schema Enforcement

When you need Claude to output structured data (JSON), enforce it at multiple levels:

### Level 1 — Prompt instruction
```
"Output a JSON object with these exact fields:
 - name (string)
 - score (integer, 0-100)
 - reason (string, max 50 words)
 
 Output only valid JSON. No markdown, no explanation."
```

### Level 2 — Schema in the prompt
```
Output must match this schema:
{
  "name": "string",
  "score": "integer between 0 and 100",
  "reason": "string"
}
```

### Level 3 — API-level enforcement
Use the `tool_use` feature or structured output mode in the API — Claude is forced to produce output matching your JSON schema at the API level. Most reliable option when available.

---

## Validation and Retry Loops

Never assume structured output is valid on the first try. Always validate and retry on failure:

```python
for attempt in range(MAX_RETRIES):
    response = claude.call(prompt)
    
    try:
        data = json.loads(response)
        validate_schema(data)   # check required fields, types, etc.
        return data             # success
    except (json.JSONDecodeError, ValidationError) as e:
        if attempt == MAX_RETRIES - 1:
            raise               # out of retries
        prompt = adjust_prompt(prompt, error=str(e))  # give Claude the error
```

### Adjusting the Prompt on Retry
Don't just retry the same prompt — include the error:

```
"Your previous response was not valid JSON. Error: 'score' field was 
 missing. Please try again and include all required fields."
```

---

## Context Placement

Where you put information in the prompt affects how Claude weighs it:

| Position | Effect |
|---|---|
| System prompt | Persistent rules, persona, constraints |
| Beginning of user message | Task framing, high-level instruction |
| Middle | Supporting context, data |
| End of user message | Final instruction / what to do now |

**Recency bias:** Instructions near the end of the prompt tend to be followed more reliably — useful for reinforcing critical output format requirements.

---

## Key Exam Points

- **XML tags** are the preferred way to structure complex prompts with Claude
- **Few-shot examples go before the real input**, never after
- **Validation + retry** is mandatory for structured output in production systems
- **Include the error message** when retrying — "try again" without context rarely helps
- **API-level enforcement** (tool_use / structured output) is more reliable than prompt-only enforcement
- **Recency bias** — repeat critical format instructions at the end of the prompt

---

## Common Trap on the Exam

> "Claude occasionally outputs JSON wrapped in markdown code blocks despite being instructed not to. What is the most reliable fix?"

Answer: Use **API-level structured output enforcement** (tool_use or structured output mode) — it forces valid JSON at the API level, bypassing the model's tendency to add formatting. Repeating the instruction in the prompt helps but is less reliable than API-level enforcement.
