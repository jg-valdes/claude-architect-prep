# Structured Output Patterns

**Exam weight:** Domain 4 – Prompt Engineering & Structured Output (20%)

---

## Why Structured Output?

Agentic systems need to pass data between steps programmatically. Natural language output requires parsing — structured output (JSON, XML) can be consumed directly by code. Reliable structured output is a prerequisite for reliable agentic pipelines.

---

## The Three Enforcement Layers

Always think in layers — each adds reliability:

```
Layer 3 (strongest): API-level enforcement (tool_use / structured output mode)
Layer 2 (medium):    Schema in the prompt + explicit format instruction
Layer 1 (weakest):   Instruction only ("output JSON")
```

Use the strongest layer available for production systems.

---

## Layer 1: Prompt Instruction Only

```
"Output your answer as a JSON object."
```

Reliability: **Low** — Claude may add markdown fences, explanation text, or deviate from the schema under complex reasoning.

Use for: quick prototyping only.

---

## Layer 2: Schema + Instruction in Prompt

Provide the exact schema and repeat the format instruction at the end:

```
Analyze the sentiment of the review below.

Output a JSON object matching this exact schema:
{
  "sentiment": "positive" | "negative" | "neutral",
  "confidence": number between 0.0 and 1.0,
  "key_phrases": array of strings (max 3)
}

Rules:
- Output only the JSON object
- No markdown code blocks
- No explanation before or after

Review: "The product works great but shipping took forever."

Output the JSON now:
```

Reliability: **Medium** — much better than instruction-only. Fails occasionally on complex inputs.

---

## Layer 3: API-Level Enforcement

### Tool Use Pattern
Define the output structure as a "tool" Claude must call. Claude is forced to produce arguments matching your JSON schema:

```python
tools = [{
    "name": "record_sentiment",
    "description": "Record the sentiment analysis result",
    "input_schema": {
        "type": "object",
        "properties": {
            "sentiment": {
                "type": "string",
                "enum": ["positive", "negative", "neutral"]
            },
            "confidence": {
                "type": "number",
                "minimum": 0.0,
                "maximum": 1.0
            },
            "key_phrases": {
                "type": "array",
                "items": {"type": "string"},
                "maxItems": 3
            }
        },
        "required": ["sentiment", "confidence", "key_phrases"]
    }
}]

response = anthropic.messages.create(
    model="claude-sonnet-4-6",
    tools=tools,
    tool_choice={"type": "auto"},
    messages=[{"role": "user", "content": prompt}]
)
```

Reliability: **High** — schema is enforced at the API level, not just in the prompt.

---

## Validation Patterns

### Schema Validation
After receiving output, validate it against your expected schema before using it:

```python
import jsonschema

schema = {
    "type": "object",
    "required": ["sentiment", "confidence"],
    "properties": {
        "sentiment": {"type": "string", "enum": ["positive", "negative", "neutral"]},
        "confidence": {"type": "number", "minimum": 0, "maximum": 1}
    }
}

jsonschema.validate(output, schema)  # raises ValidationError if invalid
```

### Business Logic Validation
Schema validity doesn't mean the output makes sense:

```python
# Schema valid but logically wrong:
{"sentiment": "positive", "confidence": 0.02, "key_phrases": ["terrible", "awful"]}

# Add business logic checks:
if output["sentiment"] == "positive" and output["confidence"] < 0.5:
    raise ValueError("Low confidence positive sentiment — needs review")
```

---

## Retry Loop Pattern (Production-Ready)

```python
MAX_RETRIES = 3

def get_structured_output(prompt, schema):
    last_error = None
    
    for attempt in range(MAX_RETRIES):
        # On retry, include the previous error in the prompt
        retry_context = ""
        if last_error:
            retry_context = f"\n\nYour previous response failed validation: {last_error}\nPlease fix this and try again."
        
        response = claude.call(prompt + retry_context)
        
        try:
            data = json.loads(response)
            jsonschema.validate(data, schema)
            return data  # success
        except Exception as e:
            last_error = str(e)
    
    raise RuntimeError(f"Failed after {MAX_RETRIES} attempts. Last error: {last_error}")
```

Key points:
- Always include the **specific error** in the retry prompt
- Cap retries — 3 is usually enough
- Raise after exhausting retries — don't silently return bad data

---

## Key Exam Points

- **Three enforcement layers** — always use the strongest available for production
- **Tool use** is the most reliable way to enforce structured output at the API level
- **Validate after receiving** — never assume output is valid even with good prompts
- **Include the error in retries** — generic "try again" rarely fixes the issue
- **Business logic validation** is separate from schema validation — both are needed
- **Cap retries** — define a max and raise after exhausting them

---

## Common Trap on the Exam

> "A pipeline step requires Claude to output a JSON array of objects. Claude occasionally outputs a JSON object instead of an array. What is the correct fix?"

Answer: Use **API-level enforcement via tool_use** with a schema that specifies `"type": "array"` — this prevents the wrong type at the API level. Updating the prompt instruction alone is unreliable for consistent enforcement.
