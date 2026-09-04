# Domain 4 — Production Scenarios

# Scenario 1 — Invoice Extraction

```mermaid
flowchart TD
    P[Invoice PDF] --> C[Claude Extraction]
    C --> J[Structured JSON]
    J --> S[Schema Validation]
    S --> B[Business Validation]
    B --> E[Evidence Check]
    E --> Q{Pass?}
    Q -- Yes --> ERP[ERP]
    Q -- No --> R{Recoverable?}
    R -- Yes --> RT[Retry with precise feedback]
    R -- No --> H[Human Review]
```

Rules:
- null for absent fields
- normalize only when unambiguous
- verify arithmetic
- preserve evidence
- never invent IDs

---

# Scenario 2 — Support Ticket Triage

Use explicit severity criteria and representative examples.

```mermaid
flowchart TD
    T[Ticket] --> P[Criteria + Examples]
    P --> C[Claude]
    C --> O[category / severity / reason]
    O --> V[Schema + Business Validation]
    V --> Q{Ambiguous?}
    Q -- No --> R[Queue Routing]
    Q -- Yes --> H[Human Triage]
```

Exam lesson: if HIGH vs MEDIUM is inconsistent, fix the decision rubric before changing infrastructure.

---

# Scenario 3 — Return Form Extraction

Fields:
- order_id
- return_reason
- requested_resolution
- comment

Use enums for finite categories. Validate:
- order exists
- return window
- item eligibility
- authorization before any refund action

---

# Scenario 4 — Email Intent Classification

```mermaid
flowchart TD
    E[Inbound Email] --> D[Untrusted Source Data]
    D --> C[Claude Classifier]
    C --> J[Structured Intent]
    J --> V[Validation]
    V --> R[Route]
```

An embedded instruction such as “ignore prior rules” remains source content, not an instruction to the application.

---

# Scenario 5 — Batch 100k Documents

```mermaid
flowchart TD
    D[100k Documents] --> B[Batch Processing]
    B --> ID[Per-item Correlation IDs]
    ID --> R[Results]
    R --> S[Success Store]
    R --> F[Failure Queue]
    F --> RT[Targeted Retry]
    RT --> H[Human Review if persistent]
```

Why batch:
- independent inputs
- offline processing
- no interactive latency requirement

---

# Scenario 6 — Multi-Pass Contract Review

```mermaid
flowchart TD
    D[Contract] --> P1[Pass 1: Extract obligations]
    P1 --> P2[Pass 2: Compare against policy]
    P2 --> P3[Pass 3: Resolve discrepancies]
    P3 --> H{High-risk ambiguity?}
    H -- Yes --> L[Legal/Human Review]
    H -- No --> O[Structured Report]
```

---

# Scenario 7 — Generator / Reviewer / Reviser

```mermaid
flowchart LR
    G[Generator] --> R[Reviewer]
    R --> Q{Rubric Pass?}
    Q -- Yes --> F[Final]
    Q -- No --> V[Reviser]
    V --> R
```

Controls:
- explicit rubric
- maximum iterations
- preserve original evidence
- escalation after unresolved disagreements

---

# Scenario 8 — Extraction with Evidence

Example:

```json
{
  "invoice_number": {
    "value": "INV-1009",
    "evidence": "Invoice No: INV-1009"
  },
  "total": {
    "value": 4500.00,
    "evidence": "Total Due: $4,500.00"
  }
}
```

Benefit: reviewers can verify the exact source support.

---

# Scenario 9 — Validation Stack

```mermaid
flowchart TD
    O[Claude Output] --> P[Parse]
    P --> S[Schema]
    S --> X[Cross-field Rules]
    X --> D[Domain Lookup]
    D --> A[Authorization]
    A --> C[Consume / Action]
```

Critical lesson: structured output does **not** bypass domain validation or authorization.

---

# Scenario 10 — Failed Field Repair

If only `invoice_number` fails, do not automatically regenerate every field. A targeted retry can be cheaper and reduce regressions.

Retry feedback:

```text
The invoice_number field failed validation.
The source contains "Invoice # INV-882".
Re-check that field and return the complete object.
Do not modify valid fields unless the source proves they are incorrect.
```

---

# Production Checklist

1. Are success criteria explicit?
2. Are examples representative and diverse?
3. Is schema narrow and versioned?
4. Are missing fields handled explicitly?
5. Are business rules validated?
6. Is evidence preserved where needed?
7. Are retries bounded?
8. Is ambiguity escalated instead of guessed?
9. Does each batch item have a correlation ID?
10. Are failed batch items retried independently?
11. Is multi-pass review justified by risk/accuracy?
12. Is source text treated as untrusted data?
13. Are prompt/schema versions logged?
14. Are evaluation cases maintained?
