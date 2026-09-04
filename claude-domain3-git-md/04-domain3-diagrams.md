# Domain 3 — Diagram Pack

## 1. Configuration selection

```mermaid
flowchart TD
    A[Instruction] --> B{Always relevant?}
    B -- Yes --> C[CLAUDE.md]
    B -- No --> D{Path-specific?}
    D -- Yes --> E[.claude/rules with paths]
    D -- No --> F{Reusable procedure?}
    F -- Yes --> G[Skill]
    F -- No --> H[One-time prompt]
```

## 2. Hard enforcement

```mermaid
flowchart TD
    R[Requirement] --> Q{Guidance or hard guarantee?}
    Q -- Guidance --> C[CLAUDE.md / rule / skill]
    Q -- Hard guarantee --> H[Hook / permission / CI policy]
```

## 3. Plan Mode

```mermaid
sequenceDiagram
    participant U as Developer
    participant C as Claude Code
    participant R as Repository

    U->>C: Request major change
    C->>R: Read / inspect
    R-->>C: Code context
    C-->>U: Proposed plan
    U->>C: Approve / revise
    C->>R: Edit after approval
    C->>R: Test
    C-->>U: Result
```

## 4. Direct execution

```mermaid
sequenceDiagram
    participant U as Developer
    participant C as Claude
    participant R as Repo

    U->>C: Small clear change
    C->>R: Inspect
    C->>R: Edit
    C->>R: Test
    C-->>U: Summary
```

## 5. Iterative refinement

```mermaid
flowchart LR
    I[Inspect] --> E[Edit]
    E --> T[Test]
    T --> Q{Pass?}
    Q -- No --> D[Diagnose]
    D --> E
    Q -- Yes --> R[Review diff]
```

## 6. CI/CD

```mermaid
flowchart TD
    G[GitHub event] --> CI[Workflow runner]
    CI --> C[Claude Code]
    C --> R[Repo]
    C --> T[Tests/checks]
    T --> P[Comment/PR/commit]
```

## 7. Path-specific rules

```mermaid
flowchart TD
    F[File path] --> M{Matches pattern?}
    M -- Yes --> R[Apply scoped rule]
    M -- No --> N[Do not load that rule]
```

## 8. Worktrees

```mermaid
flowchart LR
    R[Repo] --> W1[Worktree A]
    R --> W2[Worktree B]
    W1 --> A1[Session A]
    W2 --> A2[Session B]
```
