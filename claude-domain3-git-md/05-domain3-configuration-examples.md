# Domain 3 — Configuration Examples

# 1. Good project CLAUDE.md

```md
# Project Overview
This repository contains Spring Boot services and a React frontend.

## Build
- Backend: `./mvnw test`
- Frontend: `npm test`

## Architecture
- Controllers handle transport concerns only.
- Business logic belongs in service classes.
- Repository classes own persistence access.

## Completion
- Run relevant tests before saying a change is complete.
- Summarize files changed and tests executed.
```

# 2. Bad CLAUDE.md

```md
Be good.
Write perfect code.
Never make mistakes.
Do releases, database migrations, frontend rules, backend rules,
support workflows, deployment runbooks, all security policy...
```

Problems:
- vague
- too broad
- not verifiable
- likely too large
- mixes task-specific and evergreen guidance

---

# 3. Java path rule

```md
---
paths:
  - "src/main/java/**/*.java"
---

# Java Rules
- Prefer constructor injection.
- Use existing exception hierarchy.
- Do not swallow exceptions.
```

---

# 4. Controller rule

```md
---
paths:
  - "src/main/java/**/controller/**/*.java"
---

# Controller Rules
- Validate all request input.
- Keep controllers thin.
- Return the standard API error shape.
```

---

# 5. React rule

```md
---
paths:
  - "src/**/*.{ts,tsx}"
---

# React Rules
- Use functional components.
- Preserve accessibility attributes.
- Follow existing state-management patterns.
- Add tests for behavior changes.
```

---

# 6. Test rule

```md
---
paths:
  - "src/test/**/*.java"
  - "**/*.test.ts"
  - "**/*.spec.ts"
---

# Test Rules
- Use Arrange / Act / Assert structure where practical.
- Prefer behavior-focused assertions.
- Do not delete a failing test merely to make CI green.
```

---

# 7. Skill example — PR review

```md
---
name: review-pr
description: Review a pull request for correctness, security, test coverage, and repository conventions
---

Review the current pull request.

Steps:
1. Read the diff.
2. Identify behavioral changes.
3. Check for correctness issues.
4. Check security-sensitive changes.
5. Verify test coverage.
6. Verify repository rules.
7. Return findings ordered by severity.

For each finding include:
- severity
- file
- issue
- rationale
- suggested fix
```

---

# 8. Skill example — fix issue

```md
---
name: fix-issue
description: Investigate and fix a repository issue
---

Fix issue $0.

Workflow:
1. Understand the issue and acceptance criteria.
2. Locate relevant code.
3. Reproduce where possible.
4. Plan the smallest safe change.
5. Implement.
6. Run targeted tests.
7. Run broader tests if risk justifies it.
8. Summarize changed files, validation, and residual concerns.
```

---

# 9. Plan Mode workflow

```text
User request
    ↓
Plan Mode
    ↓
Inspect repository
    ↓
Identify affected components
    ↓
Propose steps / risks / tests
    ↓
Developer review
    ↓
Approve
    ↓
Execute
    ↓
Test
```

---

# 10. CI pseudo-workflow

```yaml
name: Claude Review

on:
  pull_request:

jobs:
  review:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write

    steps:
      - uses: actions/checkout@v4

      # Configure Claude Code action according to the
      # current official documentation.
      # Keep authentication in repository secrets.
      # Use the minimum permissions required.
```

Do not copy an old action version blindly. Check the current official Claude Code GitHub Actions documentation when implementing production CI.
