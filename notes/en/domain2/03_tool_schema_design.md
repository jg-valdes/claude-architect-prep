# Tool Schema Design

**Exam weight:** Domain 2 – Tool Design & MCP Integration (18%)

---

## Why Tool Schema Design Matters

Claude decides **when** to call a tool and **what arguments to pass** based entirely on:
1. The tool's **name**
2. The tool's **description**
3. The tool's **input schema**

A poorly designed schema leads to Claude calling the wrong tool, passing wrong arguments, or not calling a tool when it should. Good schema design is the primary lever for tool reliability.

---

## Anatomy of a Tool Definition

```json
{
  "name": "get_customer_orders",
  "description": "Retrieve all orders for a specific customer. Use this when you need to look up order history, check order status, or find a specific order for a customer.",
  "inputSchema": {
    "type": "object",
    "properties": {
      "customer_id": {
        "type": "string",
        "description": "The unique customer identifier (e.g. 'CUST-12345')"
      },
      "status": {
        "type": "string",
        "enum": ["pending", "shipped", "delivered", "cancelled"],
        "description": "Filter orders by status. Omit to return all orders."
      },
      "limit": {
        "type": "integer",
        "description": "Maximum number of orders to return. Defaults to 20.",
        "default": 20,
        "minimum": 1,
        "maximum": 100
      }
    },
    "required": ["customer_id"]
  }
}
```

---

## Rules for Good Tool Descriptions

### Be specific about when to use the tool
```
Bad:  "Gets customer data"
Good: "Retrieve all orders for a specific customer. Use when you need 
       order history, order status, or to find a specific order."
```

### Distinguish between similar tools
If you have `search_products` and `get_product_by_id`, make their descriptions clearly different:
```
search_products:    "Search products by keyword, category, or price range.
                     Use when you don't have a specific product ID."

get_product_by_id:  "Retrieve a single product by its exact ID.
                     Use when you have a product ID and need full details."
```

### Include format hints in parameter descriptions
```
Bad:  "customer_id": { "description": "The customer ID" }
Good: "customer_id": { "description": "The unique customer identifier (e.g. 'CUST-12345')" }
```

---

## Input Schema Best Practices

### Mark required vs optional fields correctly
- `"required": ["field1", "field2"]` — only include truly mandatory fields
- Optional fields should have sensible defaults

### Use enums for constrained values
```json
"status": {
  "type": "string",
  "enum": ["active", "inactive", "pending"]
}
```
This prevents Claude from passing invalid values and helps it understand the valid options.

### Use specific types, not just `string`
```
Bad:  "amount": { "type": "string" }
Good: "amount": { "type": "number", "minimum": 0 }
```

### Avoid overly broad tools
A tool that does 5 different things based on a `mode` parameter is hard for Claude to use correctly. Split it into 5 focused tools.

---

## Tool Boundary Design

**One tool = one responsibility.** Each tool should do exactly one thing clearly.

```
Bad:  manage_user(action: "create" | "update" | "delete", ...)
Good: create_user(...), update_user(...), delete_user(...)
```

Benefits:
- Claude knows exactly which tool to call for each task
- Each tool's schema can be optimized for its specific inputs
- Easier to add permissions/restrictions per tool

---

## Key Exam Points

- **Tool descriptions drive Claude's behavior** — they matter more than the code behind the tool
- **Required fields** should only include what's truly mandatory — optional fields with defaults are better UX
- **Enums** constrain inputs and help Claude understand valid values
- **One tool, one responsibility** — avoid multi-mode tools
- **Distinguish similar tools clearly** in their descriptions — Claude picks based on description, not implementation

---

## Common Trap on the Exam

> "Claude keeps passing invalid status values to a tool. What is the most effective fix?"

Answer: Add an **enum** constraint to the `status` field in the input schema — `"enum": ["active", "inactive", "pending"]`. This both prevents invalid values and explicitly tells Claude what values are valid. Fixing it only in the description is less reliable.
