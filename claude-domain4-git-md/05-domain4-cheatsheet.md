# Domain 4 — Quick Revision Cheat Sheet

## One-line definitions

- **Explicit criteria:** Define what a correct decision means.
- **Few-shot prompting:** Show representative examples of desired behavior.
- **JSON Schema:** Formal contract for fields/types/constraints.
- **Structured output:** Constrained response matching a schema.
- **Validation:** Check syntax, schema, business logic, or evidence.
- **Retry loop:** Re-run with precise feedback for recoverable failures.
- **Extraction:** Convert unstructured input into structured fields.
- **Batch processing:** Process many independent requests offline/asynchronously.
- **Multi-pass review:** Separate generation from review/revision.
- **Human review:** Escalation for unresolved ambiguity or high-risk meaning.

## Exam Mapping

| Wording | Think |
|---|---|
| HIGH vs MEDIUM inconsistent | Explicit criteria + few-shot |
| guaranteed parseable JSON | Structured output |
| fixed set of labels | Enum |
| must exist but may be unknown | Required + nullable |
| JSON valid but amount impossible | Business validation |
| missing required field | Retry with precise feedback |
| source contradictory | Human review |
| 50k independent documents | Batch |
| extract then independently verify | Multi-pass |
| auditability | Evidence/provenance |
| do not invent absent values | Null/missing rule |

## Most Important Distinction

```text
PROMPT      = meaning
SCHEMA      = shape
VALIDATION  = acceptability
REVIEW      = resolve uncertainty
```

## Retry Decision

```text
Repairable output error → Retry
Missing source data     → Do not invent
Ambiguous source        → Human review
```

## Exam Traps

1. “JSON only” always guarantees schema — false.
2. Schema guarantees truth — false.
3. Retry every ambiguity — false.
4. Guess missing values — false.
5. Few-shot examples can all be identical — poor design.
6. Batch means one giant prompt — false.
7. Confidence replaces validation — false.
8. Reviewer needs no rubric — false.
9. Structured output means authorized action — false.
10. Valid schema means safe business action — false.

## Memory Formula

**C-E-S-G-V-R-R-C**

Criteria → Examples → Schema → Generate → Validate → Retry → Review → Consume
