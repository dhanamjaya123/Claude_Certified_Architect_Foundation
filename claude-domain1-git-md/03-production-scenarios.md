# Domain 1 — Production Scenarios & Answers

# Scenario 1 — SRE Incident RCA and Remediation

## Requirement

A checkout service suddenly produces 15% HTTP 500 errors. The system must:
- inspect logs
- inspect metrics
- inspect recent deploys
- identify likely root cause
- propose remediation
- never make a production change without approval

## Recommended architecture

```mermaid
flowchart TD
    A[Alert] --> C[Coordinator]
    C --> L[Logs Agent - read only]
    C --> M[Metrics Agent - read only]
    C --> D[Deployment Agent - read only]
    L --> J[Join Evidence]
    M --> J
    D --> J
    J --> R[RCA]
    R --> G{Remediation required?}
    G -- No --> O[Report]
    G -- Yes --> H[Human Approval]
    H -- Reject --> O
    H -- Approve --> X[Remediation Tool]
    X --> V[Verify metrics + health]
    V --> S[(Checkpoint + audit)]
```

## Why
- investigation is independent → parallel
- least privilege for diagnostic agents
- coordinator combines evidence
- production write is an approval boundary
- verification follows change
- state/audit supports recovery

## Production interview/exam question
**Why not give the logs agent deployment access?**

**Answer:** It violates least privilege and increases blast radius. The diagnostic role should have only the tools required for diagnosis.

---

# Scenario 2 — Banking Payment Agent

## Requirement
User asks: “Pay ₹50,000 to beneficiary X.”

## Architecture

```mermaid
flowchart TD
    U[Payment Request] --> A[Agent]
    A --> V[Validate beneficiary + amount]
    V --> P[Authorization / limits]
    P --> H{Approval required?}
    H -- Yes --> HA[Human / OTP / policy approval]
    H -- No --> I[Create idempotency key]
    HA --> I
    I --> T[Payment API]
    T --> Q{Response known?}
    Q -- Success --> S[Persist transaction ID]
    Q -- Timeout/unknown --> C[Check status using idempotency key]
    C --> S
    S --> R[Return verified status]
```

## Key exam points
- authorization outside LLM
- idempotency
- status reconciliation after timeout
- do not repeat a financial side effect blindly

---

# Scenario 3 — Software Change Agent

## Requirement
Agent receives bug report, updates code, runs tests, creates PR.

```mermaid
flowchart LR
    B[Bug] --> C[Coordinator]
    C --> E[Code Agent]
    E --> T[Test Tool]
    T --> V{Tests pass?}
    V -- No --> E
    V -- Yes --> S[Security/Policy Verifier]
    S -->|Fail| E
    S -->|Pass| P[Create PR]
```

## Critical controls
- iteration limit on fix/test loop
- no direct production deployment
- hooks can automatically run formatter/linter
- PR creation is lower risk than production merge/deploy

---

# Scenario 4 — Customer Support Multi-Agent

## Requirement
Support requests can be billing, account-access, or technical.

```mermaid
flowchart TD
    U[Customer Query] --> R{Router}
    R --> B[Billing Agent]
    R --> A[Account Agent]
    R --> T[Technical Agent]
    B --> C[Response Coordinator]
    A --> C
    T --> C
    C --> U2[Final Answer]
```

## When this is useful
Different domains have:
- different tools
- different policies
- different knowledge
- different escalation paths

## When not useful
If all requests are simple and share the same tools/context, a single agent can be simpler.

---

# Scenario 5 — Resume a Long Workflow

## Requirement
Generate a quarterly compliance report over 500 systems. The run can last hours.

```mermaid
flowchart TD
    S[Start Run] --> D[Partition systems]
    D --> W1[Worker batch 1]
    D --> W2[Worker batch 2]
    D --> W3[Worker batch 3]
    W1 --> CP[(Checkpoint)]
    W2 --> CP
    W3 --> CP
    CP --> X{Interrupted?}
    X -- Yes --> R[Resume only incomplete batches]
    X -- No --> A[Aggregate]
    R --> A
    A --> V[Validate completeness]
    V --> F[Final report]
```

## Required state
- run ID
- batch IDs
- completed batches
- failed batches
- retry count
- evidence locations
- report version

---

# Scenario 6 — Secure Database Assistant

## Requirement
Users can ask read questions. Only DBAs may execute writes.

```mermaid
flowchart TD
    U[User Prompt] --> C[Agent]
    C --> I{Intent}
    I -- Read --> R[Read-only SQL Tool]
    I -- Write --> A[Trusted AuthZ Check]
    A -- Not DBA --> D[Deny]
    A -- DBA --> V[Validate SQL / policy]
    V --> H{High-risk?}
    H -- Yes --> P[Human approval]
    H -- No --> W[Write Tool]
    P --> W
```

## Exam answer
Never let the model decide “this user looks like a DBA.”  
Use authenticated identity and application authorization.

---

# Scenario 7 — Research Agent with Conflicting Sources

```mermaid
flowchart TD
    Q[Research Question] --> C[Coordinator]
    C --> S1[Source Search 1]
    C --> S2[Source Search 2]
    C --> S3[Source Search 3]
    S1 --> E[Evidence Table]
    S2 --> E
    S3 --> E
    E --> V[Cross-check]
    V --> G{Conflict?}
    G -- Yes --> M[More targeted research / disclose uncertainty]
    G -- No --> F[Answer with sources]
```

## Production principle
The aggregator should not silently collapse disagreement. Preserve provenance and confidence.

---

# Scenario 8 — Hooks for Enterprise Policy

## Requirement
- block reads of `.env`
- log every shell command
- run unit tests after source-code edits

```mermaid
flowchart LR
    A[Agent] --> P[PreToolUse]
    P -->|Block .env / unsafe command| X[Denied]
    P -->|Allowed| T[Tool]
    T --> O[PostToolUse]
    O --> L[Audit + tests]
    O --> A
```

## Why hooks
These requirements must fire consistently at defined lifecycle points.

---

# Scenario 9 — Agent Handoff to Human

## Requirement
An agent can prepare a legal contract summary but must not approve legal terms.

```mermaid
flowchart LR
    D[Contract] --> A[Analysis Agent]
    A --> S[Structured summary + risk flags]
    S --> H[Legal reviewer]
    H --> F[Decision]
```

## Good handoff package
- document ID/version
- extracted obligations
- risk clauses
- unanswered questions
- evidence citations
- no unauthorized conclusion

---

# Scenario 10 — Avoiding Agent Explosion

A user asks:
“Read this JSON and tell me which field is missing.”

Bad design:
```mermaid
flowchart LR
    U --> C[Coordinator] --> A1[Agent 1] --> A2[Agent 2] --> A3[Agent 3]
```

Better:
```mermaid
flowchart LR
    U[JSON] --> A[Single Agent / deterministic parser]
```

## Lesson
Architecture should match problem complexity. Orchestration is not a goal by itself.

---

# Production Design Checklist

Before shipping an agent, answer:

1. What is the goal and explicit completion condition?
2. Which steps are deterministic vs reasoning-based?
3. Which tools have side effects?
4. Where are authorization decisions made?
5. Which operations require human approval?
6. What state must survive restart?
7. How are duplicates prevented?
8. What is the maximum loop depth/turn count?
9. What happens when a tool fails?
10. How do parallel branches join?
11. How are conflicting outputs resolved?
12. What is logged/traced?
13. Which subagent gets which tools?
14. How do you validate tool inputs and outputs?
15. What is the fallback/escalation path?
