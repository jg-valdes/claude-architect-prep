# Tool Distribution & Tool Choice

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## The Tool Overload Problem

The optimal range is **4–5 tools per agent**, scoped to that agent's specific role.

Giving a single agent excessive tools degrades selection reliability and increases error rates. Tools should match agent specialization — a synthesis agent shouldn't have web search capabilities, and vice versa.

---

## Three `tool_choice` Configuration Modes

| Mode | Behavior | When to Use |
|---|---|---|
| `"auto"` (default) | Model decides whether to call a tool or respond conversationally | General use — no output guarantee |
| `"any"` | Model MUST call a tool but selects which one | When structured output is required but you don't care which tool |
| Specific tool name | Model MUST call that specific named tool | Enforcing workflow ordering, mandatory extraction steps |

**Critical:** Use `"auto"` when structured output is required = wrong answer. Use `"any"` or forced selection.

---

## Scoped Cross-Role Tools

Rather than routing all requests through a coordinator (adding 2–3 round trips), agents can have constrained versions of needed capabilities.

**Example:** Give a synthesis agent a limited `verify_fact` tool for simple lookups while routing complex verifications to the coordinator. Avoids unnecessary latency.

---

## Least Privilege Tool Design

Replace generic tools with constrained alternatives:
- Generic: `fetch_url` (can fetch anything)
- Constrained: `load_document` (validates document URLs only)

This prevents misuse and clarifies tool purpose.

---

## Example Role-Based Distribution

| Agent | Tools (4–5 each) |
|---|---|
| Web Search | `search_web`, `fetch_page`, `extract_links`, `save_snippet` |
| Document Analysis | `extract_metadata`, `extract_data_points`, `summarize_content`, `verify_claim` |
| Synthesis | `compile_report`, `verify_fact` (scoped), `format_citation`, `assess_coverage` |
| Coordinator | `Agent`, `review_output`, `request_revision` |

---

## stdio vs. SSE Transport

| Transport | Use Case | Authentication |
|---|---|---|
| `stdio` | Local servers, same-machine processes | No auth needed (process isolation) |
| `SSE` | Remote servers, multi-client setups | Requires authentication (network-exposed) |

Select `stdio` for local development tools. Select `SSE` when the MCP server runs on a remote host or needs to serve multiple clients simultaneously.

---

## Key Exam Points

- Optimal tool count per agent: **4–5**, scoped to role
- `"auto"` does NOT guarantee structured output — use `"any"` or specific tool name
- Least privilege: prefer constrained tools over generic ones
- Scoped cross-role tools reduce coordinator round-trips
- `stdio` = local, `SSE` = remote (requires auth)

---

## Common Exam Traps

- Using `tool_choice: "auto"` when structured output is required
- Assigning 15+ tools expecting reliable selection
- Generic tools enabling unintended agent behaviors
- Routing simple lookups through coordinators unnecessarily
