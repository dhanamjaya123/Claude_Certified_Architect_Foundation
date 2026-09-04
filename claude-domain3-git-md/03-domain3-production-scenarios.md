# Domain 3 — Production Scenarios

# Scenario 1 — Enterprise Spring Boot repository

## Goal
Make Claude Code consistently follow project architecture.

```mermaid
flowchart TD
    C[Claude Code] --> M[Project CLAUDE.md]
    M --> R1[Java rules]
    M --> R2[API path rules]
    M --> R3[Test rules]
    C --> S[Skills]
    S --> V[Security-fix workflow]
```

Recommended:
- project-wide commands in `CLAUDE.md`
- controller-specific standards in path rules
- long security-remediation procedure in a skill
- hard security blocks in hooks/CI

---

# Scenario 2 — Major microservice refactor

## Request
Split one service into three bounded services.

```mermaid
flowchart TD
    U[Request] --> P[Plan Mode]
    P --> D[Dependency map]
    D --> A[Architecture proposal]
    A --> H[Human review]
    H --> E1[Increment 1]
    E1 --> T1[Test]
    T1 --> E2[Increment 2]
    E2 --> T2[Test]
    T2 --> PR[PR]
```

Why:
- broad
- architectural
- risky
- requires review before disk changes

---

# Scenario 3 — Small production bug

Request:
Fix one null check.

```mermaid
flowchart LR
    B[Bug] --> R[Read relevant code]
    R --> E[Edit]
    E --> T[Targeted test]
    T --> D[Diff review]
```

Use direct execution if the fix is clear and permissions allow.

---

# Scenario 4 — Path-specific rules in monorepo

```text
repo/
├── frontend/
├── backend/
├── infra/
└── .claude/rules/
```

Frontend:
```yaml
paths:
  - "frontend/**/*.{ts,tsx}"
```

Backend:
```yaml
paths:
  - "backend/**/*.java"
```

Infra:
```yaml
paths:
  - "infra/**/*.tf"
```

Benefit:
Each task gets relevant instructions instead of the entire monorepo's rules.

---

# Scenario 5 — PR review skill

```mermaid
flowchart TD
    P[PR Diff] --> S[/review-pr]
    S --> A[Architecture]
    S --> T[Tests]
    S --> SEC[Security]
    S --> Q[Quality]
    A --> O[Findings]
    T --> O
    SEC --> O
    Q --> O
```

Return:
- severity
- file/line
- issue
- rationale
- suggested correction

---

# Scenario 6 — Vulnerability remediation skill

Workflow:
1. read scanner result
2. identify vulnerable dependency/path
3. inspect exploitability
4. propose minimal fix
5. make change
6. run targeted tests
7. rerun scanner/check
8. summarize

```mermaid
flowchart LR
    F[Finding] --> A[Analyze]
    A --> P[Plan fix]
    P --> E[Edit]
    E --> T[Test]
    T --> S[Security check]
    S --> R[Result]
```

---

# Scenario 7 — CI code review

```mermaid
flowchart TD
    PR[PR opened] --> G[GitHub Action]
    G --> C[Claude Code]
    C --> R[Read diff]
    R --> O[Review findings]
    O --> PR
    PR --> D[Required deterministic checks]
    D --> H[Human merge decision]
```

Claude is one layer, not the only layer.

---

# Scenario 8 — CI issue-to-PR automation

```mermaid
flowchart TD
    I[Issue] --> A[Claude automation]
    A --> B[Branch]
    B --> C[Code change]
    C --> T[Tests]
    T --> P[Pull Request]
    P --> H[Human review]
```

Safety:
- no direct protected-branch write
- narrow token scope
- required tests
- review before merge

---

# Scenario 9 — Personal vs team configuration

Team:
- Java version
- build command
- architecture

→ project `CLAUDE.md`

Developer only:
- localhost port
- preferred test fixture

→ `CLAUDE.local.md`

Never:
- passwords
- tokens
- private keys

→ use secret storage

---

# Scenario 10 — Iterative refinement after CI failure

```mermaid
flowchart TD
    PR[PR] --> CI[CI fails]
    CI --> L[Read logs]
    L --> H[Hypothesis]
    H --> E[Fix]
    E --> T[Run tests]
    T --> Q{Pass?}
    Q -- No --> L
    Q -- Yes --> U[Update PR]
```

---

# Scenario 11 — Parallel work with worktrees

```mermaid
flowchart TD
    R[Repository] --> W1[worktree: auth-fix]
    R --> W2[worktree: ui-feature]
    W1 --> C1[Claude Session A]
    W2 --> C2[Claude Session B]
```

Reason:
Isolate working directories and branches so edits do not collide.

---

# Scenario 12 — Configuration governance

A large team should periodically review:

- root `CLAUDE.md`
- local/nested `CLAUDE.md` files
- `.claude/rules/`
- skills
- hooks
- CI workflows
- permissions
- stale instructions

```mermaid
flowchart LR
    C[Configuration] --> R[Periodic review]
    R --> S[Remove stale/conflicting rules]
    S --> V[Version control]
```
