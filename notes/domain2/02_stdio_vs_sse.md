# stdio vs SSE Transport

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## The Two Transports Side by Side

| | stdio | SSE (Server-Sent Events) |
|---|---|---|
| **Where server runs** | Local subprocess | Remote HTTP server |
| **Communication** | stdin / stdout | HTTP long-polling |
| **Multiple clients** | No — one client per process | Yes — many clients, one server |
| **Latency** | Minimal (local) | Network latency |
| **Authentication** | Not needed (local) | Required (tokens, API keys) |
| **Setup complexity** | Low | Higher |
| **Best for** | Local tools, dev environments | Shared services, cloud tools |

---

## stdio Transport

The MCP server is launched as a **child process** by the client. They talk through standard input/output streams.

```
Claude Code
  └── spawns → MCP Server process
                 ↕ stdin/stdout JSON-RPC messages
```

**When to use stdio:**
- File system tools (read/write local files)
- Local database connections
- CLI tool wrappers
- Development and testing
- Tools that only one user needs

**Advantages:**
- Zero network overhead
- No authentication needed — OS-level process isolation
- Simple setup — just a command to run
- Automatically scoped to one user/session

**Disadvantages:**
- Can't be shared across multiple users or machines
- Requires the server to be installed locally

---

## SSE Transport

The MCP server runs as a standalone HTTP server. Clients connect via HTTP and receive responses as Server-Sent Events (a streaming HTTP mechanism).

```
Claude Code ──HTTP request──→ MCP Server (remote)
Claude Code ←──SSE stream──── MCP Server (remote)
```

**When to use SSE:**
- Shared company tools (multiple team members use the same server)
- Cloud-hosted data sources
- APIs that need central auth management
- High-availability production tools
- Multi-tenant environments

**Advantages:**
- Shared across many clients simultaneously
- Centrally managed — update once, all clients benefit
- Can be deployed to cloud infrastructure

**Disadvantages:**
- Requires authentication (tokens, OAuth, API keys)
- Network latency on every tool call
- More complex to set up and secure
- Additional attack surface (exposed HTTP endpoint)

---

## Authentication in SSE

Since SSE servers are exposed over HTTP, they require authentication:

**Common patterns:**
- **Bearer tokens** — client sends `Authorization: Bearer <token>` header
- **API keys** — client sends key in header or query param
- **OAuth 2.0** — full OAuth flow for user-delegated access
- **mTLS** — mutual TLS for machine-to-machine auth

The MCP server is responsible for validating auth on every incoming request.

---

## Choosing the Right Transport

```
Is the tool only needed on the local machine?
  Yes → stdio

Is the tool shared across a team or multiple machines?
  Yes → SSE

Is this for development/prototyping?
  Yes → stdio (simpler to set up)

Is this for production with multiple users?
  Yes → SSE
```

---

## Key Exam Points

- **stdio** = local subprocess, no auth, one client — simple and fast
- **SSE** = remote HTTP, requires auth, many clients — scalable and shareable
- **Auth is an SSE concern only** — stdio doesn't need it (OS isolation is sufficient)
- SSE introduces **network latency** — factor this into tool design for latency-sensitive use cases
- Both transports use the **same MCP protocol** — only the transport layer differs

---

## Common Trap on the Exam

> "A company wants to give all 50 developers access to a shared internal database tool via MCP. Which transport should they use?"

Answer: **SSE** — it's a shared service with multiple clients. stdio only supports one client per process instance and requires local installation on every machine.
