# MCP Fundamentals

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## What Is MCP?

**Model Context Protocol (MCP)** is an open protocol that standardizes how AI models connect to external tools, data sources, and services. Think of it as a universal adapter — instead of building a custom integration for every tool, you build one MCP server and any MCP-compatible client (like Claude) can use it.

```
Claude (MCP Client) ←→ MCP Protocol ←→ MCP Server ←→ External Tool/Data
```

---

## Core Concepts

### MCP Server
- Exposes **tools**, **resources**, and **prompts** to the client
- Runs as a separate process (local) or remote service
- Defines what capabilities are available and how to call them
- Is responsible for actually executing the tool logic

### MCP Client
- The AI model (Claude) or the host application
- Discovers available tools from the server
- Calls tools by sending structured requests
- Receives structured responses

### Tools
Functions the model can call. Defined with a name, description, and input schema:

```json
{
  "name": "search_database",
  "description": "Search the product database by keyword",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": { "type": "string", "description": "Search keyword" },
      "limit": { "type": "integer", "description": "Max results", "default": 10 }
    },
    "required": ["query"]
  }
}
```

### Resources
Static or dynamic data the server exposes (files, database records, API responses). Resources are read-only — unlike tools, they don't perform actions.

### Prompts
Reusable prompt templates the server exposes to the client. Less common in practice.

---

## The Two Transports

MCP supports two transport mechanisms:

### stdio (Standard I/O)
- Client and server communicate via stdin/stdout
- Server runs as a **local subprocess**
- Simple to set up — no networking required
- Best for local tools (file system access, local databases, CLI tools)

### SSE (Server-Sent Events)
- Client and server communicate over **HTTP**
- Server runs as a **remote service**
- Supports multiple simultaneous clients
- Requires network — introduces latency and auth concerns
- Best for shared services, remote APIs, cloud tools

---

## Key Exam Points

- MCP is a **protocol**, not a library — it defines the communication standard
- **Tools** perform actions; **resources** expose data read-only
- **stdio** = local subprocess; **SSE** = remote HTTP service
- The **tool description** is critical — Claude uses it to decide when and how to call the tool
- MCP servers are **language-agnostic** — can be written in Python, TypeScript, Go, etc.

---

## Common Trap on the Exam

> "What is the primary purpose of MCP?"

Answer: To provide a **standardized protocol** for connecting AI models to external tools and data sources — not to improve model performance, not to add memory, not to enable multi-agent systems (though it can support those use cases).
