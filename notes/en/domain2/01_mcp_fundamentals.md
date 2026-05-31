# MCP Fundamentals

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## What Is MCP?

The Model Context Protocol (MCP) extends Claude's capabilities by connecting it to external systems — databases, APIs, development tools. It defines a standard way to expose tools, resources, and prompts to Claude.

---

## Structured Error Responses

When MCP tools fail, structured error responses enable agents to recover intelligently. The MCP protocol's `isError` flag signals failures.

### Four Error Categories

| Category | Description | `isRetryable` | Recovery |
|---|---|---|---|
| **Transient** | Timeout, rate limit, service unavailable | `true` | Retry with backoff |
| **Validation** | Malformed request, invalid format, missing fields | `true` | Fix input and retry |
| **Business** | Policy violation, rule conflict (valid request, wrong constraint) | `false` | Escalate or use alternative workflow |
| **Permission** | Access denied, insufficient credentials | `false` | Escalate or obtain different credentials |

**Critical:** Business errors have `isRetryable: false` — retrying produces identical failures.

---

## Access Failure vs. Valid Empty Result

| Situation | `isError` | `resultCount` | Action |
|---|---|---|---|
| Tool cannot reach data source (timeout, auth failure) | `true` | — | Consider retry |
| Tool executed successfully, found no matches | `false` | `0` | No retry — this IS the answer |

This distinction **prevents wasted retries**. Never retry a valid empty result.

---

## MCP Server Configuration

### Scoping Hierarchy

| File | Scope | Version-Controlled? |
|---|---|---|
| `.mcp.json` (project root) | Whole team | Yes — shared via git |
| `~/.claude.json` | Personal | No — local only |

**Key principle:** All tools from all configured servers (both project and user level) are discovered at connection time and available simultaneously.

### Environment Variable Expansion

`.mcp.json` supports `${VARIABLE_NAME}` syntax to keep credentials out of version control:

```json
{
  "mcpServers": {
    "jira": {
      "command": "npx",
      "args": ["@company/mcp-jira", "--token", "${JIRA_TOKEN}"]
    }
  }
}
```

Each developer sets their own tokens locally.

---

## MCP Resources

Resources expose content catalogs to agents **without requiring exploratory tool calls**:
- Issue summaries with titles and statuses
- Documentation hierarchies
- Database schemas with table names and relationships

Resources reduce unnecessary queries by making information immediately available.

---

## Build vs. Use Decision

**Use community servers for:**
- Standard integrations (Jira, GitHub, Slack, Notion)
- Tested solutions requiring minimal maintenance

**Build custom servers only when:**
- Team has specific workflows community servers can't handle
- Custom business logic is needed in the tool layer
- Integration with proprietary internal systems

---

## Enhancing MCP Tool Descriptions

Sparse descriptions cause agents to prefer built-in tools with richer documentation. Enhanced descriptions should be **3–5 sentences** explaining capabilities, outputs, use cases, and comparisons to alternatives.

---

## Key Exam Points

- Team-wide config → `.mcp.json` (project root, version-controlled)
- Personal/experimental config → `~/.claude.json` (never committed)
- Business errors are **not retryable** — this is frequently tested
- Valid empty result ≠ access failure — know the difference
- Always evaluate community MCP servers before building custom ones
- Commit `.mcp.json` with env var references, never raw credentials

---

## Common Exam Trap

> "Should the Jira MCP config go in `.mcp.json` or `~/.claude.json`?"

Answer: `.mcp.json` in the project root — it's team-wide and should be version-controlled. `~/.claude.json` is for personal/experimental servers only.
