# Domain 2 — Production Scenarios & Answers

# Scenario 1 — Order Management MCP

## Requirements

Agent must:
- retrieve order
- cancel eligible order
- explain failure
- avoid duplicate side effects

```mermaid
flowchart TD
    U[User] --> C[Claude]
    C --> MC[MCP Client]
    MC --> MS[Order MCP Server]
    MS --> G[get_order]
    G --> API[Order API]
    API --> G
    G --> C2{Cancelable?}
    C2 -- No --> E[Structured domain error]
    C2 -- Yes --> X[cancel_order]
    X --> V[Validate auth + state]
    V --> API
    API --> R[Structured result]
    R --> C
```

## Why split `get_order` and `cancel_order`?

They have different:
- semantics
- side effects
- permissions
- validation requirements
- retry risk

---

# Scenario 2 — SRE MCP Integration

Tools:
- `get_logs`
- `get_metrics`
- `get_deployment_history`
- `restart_service`

```mermaid
flowchart TD
    C[Coordinator] --> A[Diagnosis Agent]
    A --> L[get_logs]
    A --> M[get_metrics]
    A --> D[get_deployment_history]
    A --> R[RCA]
    R --> H{Restart needed?}
    H -- Yes --> P[Approval]
    P --> T[restart_service]
    T --> V[Verify health]
```

Diagnosis agent gets read tools. Remediation path gets write tool only when authorized.

---

# Scenario 3 — File Analysis

Goal: find Java controllers containing a deprecated call.

```mermaid
flowchart LR
    G[Goal] --> GL[Glob **/*Controller.java]
    GL --> GR[Grep deprecatedCall]
    GR --> R[Read matched files]
    R --> A[Analyze]
```

Why?
- Glob narrows paths
- Grep searches content
- Read retrieves full relevant files

---

# Scenario 4 — Secure Bash

Need to check disk usage: low-risk read operation.

Need to recursively delete production data: destructive/high-risk.

```mermaid
flowchart TD
    B[Bash request] --> C{Risk}
    C -- Low/read-only --> E[Execute under policy]
    C -- High/destructive --> A[Block or require approval]
```

Tool capability never implies unrestricted permission.

---

# Scenario 5 — Customer Profile Tool Design

Bad:

```text
customer(action, value)
```

Better:

```text
get_customer_profile(customer_id)
update_customer_phone(customer_id, phone)
list_customer_orders(customer_id)
```

Benefits:
- clearer tool selection
- clearer schema
- clearer permissions
- easier telemetry

---

# Scenario 6 — Structured Error Recovery

Call:

```text
get_order(order_id="123")
```

Result:

```json
{
  "success": false,
  "error": {
    "code": "INVALID_FORMAT",
    "message": "order_id must have format ORD-<number>",
    "retryable": false
  }
}
```

The model can distinguish “fix the input” from “wait and retry.”

---

# Scenario 7 — Rate-Limited Search

```mermaid
flowchart TD
    S[search_docs] --> E[429]
    E --> R[retry_after]
    R --> B[Backoff]
    B --> T{Retry budget left?}
    T -- Yes --> S
    T -- No --> X[Return failure / escalate]
```

Avoid immediate loops and infinite retries.

---

# Scenario 8 — Payment Timeout

```mermaid
flowchart TD
    P[create_payment] --> T[Timeout]
    T --> U{Outcome unknown}
    U --> Q[get_payment_by_idempotency_key]
    Q --> S{Found?}
    S -- Yes --> D[Return existing payment]
    S -- No --> R[Safe retry if policy allows]
```

### Exam point
Timeout is not proof of failure.

---

# Scenario 9 — Documentation Resource

```mermaid
flowchart TD
    C[Claude] --> MC[MCP Client]
    MC --> MS[Docs MCP Server]
    MS --> R[Resource: docs://payments/refunds]
    R --> D[Refund policy]
    D --> C
```

Use a resource because this capability is readable context.

---

# Scenario 10 — Tool Contract Versioning

Version 1:

```json
{"order_id":"ORD-1"}
```

Version 2 suddenly requires:

```json
{"tenant_id":"T1","order_id":"ORD-1"}
```

This can break clients.

Production approach:
- prefer compatible evolution
- version breaking changes
- migrate deliberately
- test compatibility

---

# Scenario 11 — Agent-Specific MCP Servers

```mermaid
flowchart TD
    C[Coordinator] --> HR[HR Agent]
    C --> IT[IT Agent]
    C --> FIN[Finance Agent]

    HR --> HRS[HR MCP Server]
    IT --> ITS[IT MCP Server]
    FIN --> FS[Finance MCP Server]
```

Benefits:
- policy isolation
- smaller tool catalogs
- cleaner permissions
- less accidental cross-domain use

---

# Production Tool Checklist

Before exposing a tool, ask:

1. Is the name unambiguous?
2. Does the description say when to use it?
3. Are required inputs really required?
4. Are enums/constraints represented?
5. Is domain validation present?
6. Is authorization enforced?
7. Is it read-only or side-effecting?
8. Is output structured?
9. Are errors actionable?
10. Is retryability explicit?
11. Are writes idempotent/safe to resume?
12. Are secrets hidden?
13. Is telemetry captured?
14. Does only the correct agent get the tool?
