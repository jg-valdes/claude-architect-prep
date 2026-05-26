# Context Window Management

**Exam weight:** Domain 5 – Context Management & Reliability (15%)

---

## What Is the Context Window?

The context window is the total amount of text (tokens) a model can process in a single call — including the system prompt, conversation history, tool results, and the response being generated.

When context fills up:
- Older information gets truncated or lost
- The model loses access to earlier parts of the conversation
- Performance degrades as the window approaches its limit

**Key constraint:** Claude cannot access information outside its current context window unless it uses a tool to retrieve it.

---

## Strategies for Managing Context

### 1. Summarization
Instead of keeping the full history, summarize completed work and replace the raw history with the summary.

```
Raw history (10,000 tokens)
  → Summarize into key findings (500 tokens)
  → Continue with summary + new context
```

Use when: long conversations or multi-step tasks where early details are no longer needed verbatim.

### 2. External Memory
Store information outside the context window and retrieve it when needed via tools.

```
Agent reads file A → extracts key data → saves to external store
Later: agent queries external store → retrieves only what's needed
```

Tools for external memory: file reads, database queries, vector search, key-value stores.

Use when: the total information needed exceeds one context window.

### 3. Chunking
Break large inputs into smaller pieces and process them one chunk at a time.

```
Large document (100 pages)
  → Process pages 1-10 → extract findings
  → Process pages 11-20 → extract findings
  → ...
  → Aggregate all findings at the end
```

Use when: inputs are too large to fit in a single context window.

### 4. Selective Context Loading
Only load the context relevant to the current subtask — not everything.

```
Bad:  Pass entire codebase to every subagent
Good: Pass only the files relevant to each subagent's specific task
```

Use when: you have more context available than any one agent needs.

### 5. Structured Context
Format context so the most important information is prominent and easy to parse.

```markdown
## Current Goal
Fix the authentication bug in src/auth.ts

## Relevant Files
- src/auth.ts (the buggy file)
- src/middleware/session.ts (dependency)

## What We've Tried
- Checked token expiry logic → not the issue
- Checked cookie settings → not the issue

## Current Hypothesis
The JWT secret is not being loaded from env correctly
```

Structured context helps the model prioritize the right information.

---

## Token Budgeting

In multi-step agentic workflows, plan your token budget:

| Component | Typical allocation |
|---|---|
| System prompt | 1,000–5,000 tokens |
| Task context | 5,000–20,000 tokens |
| Tool results | Variable — summarize if large |
| Response | Reserve 2,000–4,000 tokens |

If tool results are large (e.g. a 50-page document), summarize or chunk before including in context.

---

## Key Exam Points

- **Summarization** is the primary tool for managing growing conversation history
- **External memory** (files, databases) is the solution when total information exceeds one context window
- **Chunking** is the pattern for processing inputs too large to fit in one call
- **Selective context loading** keeps subagent calls lean and focused
- Context window limits are a **hard constraint** — design around them, don't ignore them

---

## Common Trap on the Exam

> "An agentic system needs to process a 500-page document and extract key facts. The document is too large for one context window. What is the correct approach?"

Answer: **Chunking** — split the document into sections, process each section independently, extract findings, then aggregate. Do not try to fit the whole document into one call.
