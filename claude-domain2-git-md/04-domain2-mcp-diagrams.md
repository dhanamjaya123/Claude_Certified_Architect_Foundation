# Domain 2 — MCP & Tool Design Diagrams

# 1. End-to-end tool loop

```mermaid
sequenceDiagram
    participant U as User
    participant C as Claude
    participant A as Application
    participant T as Tool

    U->>C: Request
    C-->>A: Tool call + JSON arguments
    A->>A: Validate + authorize
    A->>T: Execute
    T-->>A: Result / structured error
    A-->>C: Tool result
    C-->>U: Continue / final answer
```

# 2. MCP architecture

```mermaid
flowchart LR
    U[User] --> H[Host]
    H --> C[MCP Client]
    C --> S[MCP Server]
    S --> T[Tools]
    S --> R[Resources]
    S --> P[Prompts]
```

# 3. Tool vs resource

```mermaid
flowchart TD
    N[Need external capability] --> Q{Action or context?}
    Q -- Action / operation --> T[MCP Tool]
    Q -- Readable data / URI --> R[MCP Resource]
```

# 4. Retry decision

```mermaid
flowchart TD
    E[Error] --> A{Input/business error?}
    A -- Yes --> C[Correct input / stop]
    A -- No --> B{Transient?}
    B -- Yes --> S{Side-effecting call?}
    S -- No --> R[Bounded retry]
    S -- Yes --> Q[Check operation status/idempotency]
    B -- No --> X[Escalate / fail]
```

# 5. Tool distribution

```mermaid
flowchart TD
    C[Coordinator] --> A1[Analysis Agent]
    C --> A2[Remediation Agent]
    C --> A3[Support Agent]
    A1 --> T1[Read-only tools]
    A2 --> T2[Change tools]
    A3 --> T3[Customer tools]
```

# 6. Built-in selection

```mermaid
flowchart TD
    G[Need] --> Q{What kind?}
    Q -->|Known file content| R[Read]
    Q -->|Text search| GR[Grep]
    Q -->|Filename/path search| GL[Glob]
    Q -->|Modify file| E[Edit]
    Q -->|Create/replace file| W[Write]
    Q -->|Run command| B[Bash]
```

# 7. Secure write tool

```mermaid
flowchart LR
    C[Claude] --> S[Schema validation]
    S --> D[Domain validation]
    D --> A[Authorization]
    A --> H{High risk?}
    H -- Yes --> P[Human approval]
    H -- No --> E[Execute]
    P --> E
    E --> O[Structured output]
```

# 8. MCP production service

```mermaid
flowchart LR
    A[AI Host] --> C[MCP Client]
    C --> G[Gateway / Auth]
    G --> S[MCP Server]
    S --> B[Business Service]
    B --> DB[(Database)]
    S --> O[(Logs / Metrics / Traces)]
```
