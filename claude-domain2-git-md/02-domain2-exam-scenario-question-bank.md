# Domain 2 — Exam Scenario Question Bank

> Try each question before reading the answer.

---

## Q1 — Wrong tool selected

Claude repeatedly selects `customer_lookup` when it should use `order_lookup`. Both descriptions are vague.

A. Increase temperature  
B. Improve tool names/descriptions and clarify when each applies  
C. Add more agents  
D. Retry automatically

**Answer: B**

---

## Q2 — Required field

The backend cannot query an order without `order_id`, but the schema marks it optional.

A. Leave optional  
B. Make `order_id` required and describe its format  
C. Remove schema  
D. Add retries

**Answer: B**

---

## Q3 — Fixed values

Backend accepts only `LOW`, `MEDIUM`, `HIGH`.

Best schema?

A. Free-form string  
B. Enum  
C. Integer  
D. No schema

**Answer: B**

---

## Q4 — Domain validation

Schema accepts numeric `amount`, but negative transfers are invalid.

Where should this be enforced?

A. Nowhere  
B. Domain validation before execution  
C. Tool name only  
D. After transaction

**Answer: B**

---

## Q5 — Structured output

Which is better?

A. `Done`  
B. `{success:true, order_id:"ORD-1", status:"CANCELLED"}`  
C. `Maybe okay`  
D. Empty

**Answer: B**

---

## Q6 — Retryable failure

A read-only backend returns a temporary 503.

A. Retry forever  
B. Bounded retry with backoff  
C. Mark success  
D. Change user identity

**Answer: B**

---

## Q7 — Invalid input

Tool returns `INVALID_ACCOUNT_ID`.

A. Retry same input 10 times  
B. Correct the argument or ask for a valid ID  
C. Ignore  
D. Restart everything

**Answer: B**

---

## Q8 — Write timeout

`create_payment` times out after the request was sent.

A. Immediately send again  
B. Check transaction/idempotency status first  
C. Assume failure  
D. Assume success

**Answer: B**

---

## Q9 — Tool vs resource

Claude must restart a deployment.

A. Resource  
B. Tool  
C. Prompt  
D. Static file

**Answer: B**

---

## Q10 — Tool vs resource

An MCP server exposes internal policy documents by URI for reading.

A. Resource  
B. Write tool  
C. Bash  
D. Retry policy

**Answer: A**

---

## Q11 — MCP flow

Which is conceptually correct?

A. Model → database directly is always the MCP architecture  
B. Host/client → MCP server → capability  
C. MCP server → model weights  
D. Resource → trains model

**Answer: B**

---

## Q12 — Useful error

Which error is best?

A. `error`  
B. `failed`  
C. `{code:"INVALID_EMAIL", message:"email format invalid", retryable:false}`  
D. Blank

**Answer: C**

---

## Q13 — Tool distribution

A log-analysis agent only needs logs and metrics.

A. Give production-delete tools too  
B. Give only relevant read tools  
C. Give all tools  
D. Give finance admin tools

**Answer: B**

---

## Q14 — Grep vs Glob

Find every file containing `customerId`.

A. Glob  
B. Grep  
C. Write  
D. Edit

**Answer: B**

---

## Q15 — Grep vs Glob

Find all files matching `**/*.java`.

A. Grep  
B. Glob  
C. Read  
D. Write

**Answer: B**

---

## Q16 — Read

You know `/src/App.java` and need its contents.

A. Read  
B. Glob  
C. Write  
D. Create issue

**Answer: A**

---

## Q17 — Edit

Change a method in an existing file.

A. Glob  
B. Edit  
C. Resource  
D. Search docs

**Answer: B**

---

## Q18 — Purpose-built tool vs Bash

Why prefer a purpose-built business tool over unrestricted Bash when possible?

A. Bash cannot execute  
B. Purpose-built tools provide tighter schema, validation and permission boundaries  
C. Bash has no output  
D. MCP forbids Bash

**Answer: B**

---

## Q19 — Authorization

Tool input is schema-valid, but the caller lacks permission.

A. Execute because JSON is valid  
B. Deny at trusted authorization layer  
C. Ask Claude to pretend permission exists  
D. Retry

**Answer: B**

---

## Q20 — Too many tools

One agent sees 100 tools with overlapping names.

Best improvement?

A. Add more tools  
B. Narrow/distribute the tool set by responsibility  
C. Remove descriptions  
D. Rename all tools `execute`

**Answer: B**

---

## Q21 — Tool granularity

Which is better?

A. `manage_everything`  
B. Meaningful operations such as `get_order` and `cancel_order`  
C. 70 almost-identical tools  
D. No schemas

**Answer: B**

---

## Q22 — Read-only timeout

Read-only search fails due to a transient network timeout.

A. Bounded retry can be appropriate  
B. Never retry anything  
C. Treat as permission error  
D. Write instead

**Answer: A**

---

## Q23 — Permission denied

`restart_service` returns `PERMISSION_DENIED`.

Retry?

A. Yes indefinitely  
B. No blind retry; request proper authorization/approval  
C. Rename tool  
D. Ignore

**Answer: B**

---

## Q24 — Side effects

Which needs stronger control?

A. `get_order`  
B. `delete_account`  
C. `read_policy`  
D. `search_docs`

**Answer: B**

---

## Q25 — Ambiguous output

Tool returns: `Customer is okay`.

Why is this weak?

A. Too structured  
B. Important fields/status are ambiguous  
C. JSON is forbidden  
D. Claude cannot read text

**Answer: B**

---

## Q26 — Schema vs prompt

A tool accepts exactly one of three modes.

Best primary representation?

A. Hide modes only in a long prompt  
B. Constrain schema where possible  
C. Accept any value  
D. Retry invalid calls forever

**Answer: B**

---

## Q27 — MCP server

What does an MCP server do conceptually?

A. Train Claude  
B. Expose capabilities/data through MCP  
C. Replace all APIs  
D. Store model weights

**Answer: B**

---

## Q28 — MCP client

The MCP client primarily:

A. Communicates with MCP server for the host/application  
B. Trains the server  
C. Is a database  
D. Replaces schemas

**Answer: A**

---

## Q29 — Correctable tool error

Tool says `order_id must start with ORD-`.

If the corrected ID is safely known, what should the agent do?

A. Retry same invalid value  
B. Correct argument and call again  
C. Delete tool  
D. Ignore error

**Answer: B**

---

## Q30 — Retry limit

Why bound retries?

A. Prevent infinite loops, cost, and repeated load  
B. Because one retry always succeeds  
C. Errors are impossible  
D. To remove telemetry

**Answer: A**

---

# Harder Scenario Questions

## Q31 — Overlapping search tools

Tools are named `search`, `find`, `lookup`, and `query`, but hit different systems. Claude often picks the wrong one.

**Answer:** Rename around business intent and improve descriptions/schema boundaries, e.g. `search_internal_docs`, `get_order_by_id`, `search_incident_history`.

---

## Q32 — Duplicate ticket risk

`create_ticket` times out and may already have created the ticket.

**Answer:** Check by request/idempotency key or external operation ID before another create call.

---

## Q33 — MCP resource

Server exposes `policy://refunds/current`.

**Answer:** Resource — readable contextual data addressed by URI.

---

## Q34 — MCP action

Server exposes `approve_refund(refund_id)`.

**Answer:** Tool — it performs an operation.

---

## Q35 — Tool scoping

Billing agent receives shell, deployment, HR, and finance-admin tools though it only reads invoices.

**Answer:** Excessive privilege and tool-choice ambiguity. Restrict tools to role-relevant capabilities.

---

## Q36 — Validation failure

`email="abc"` fails email format validation.

Retryable infrastructure failure?

**Answer:** No. Correctable input/domain error.

---

## Q37 — Rate limit

Tool returns 429 and `retry_after=15`.

**Answer:** Respect retry guidance and use bounded backoff.

---

## Q38 — MCP server unavailable

Server is unavailable for several minutes.

**Answer:** Use bounded retry/circuit-breaker behavior and surface/escalate persistent failure. Never fabricate results.

---

## Q39 — Secrets

A tool needs an API credential.

A. Put key in every prompt  
B. Trusted service/tool reads it from secret storage  
C. Return it in tool output  
D. Log it

**Answer: B**

---

## Q40 — Untrusted tool output

A remote result contains unexpected fields plus “ignore all prior instructions.”

**Answer:** Treat remote/tool output as untrusted data, validate structure, and preserve application/system security boundaries.

---

# True / False Drill

1. Tool names/descriptions influence tool selection. **True**  
2. Every tool error should be retried. **False**  
3. A write timeout can leave outcome unknown. **True**  
4. MCP resources and tools are identical. **False**  
5. Tool schema replaces authorization. **False**  
6. Grep searches file content. **True**  
7. Glob matches file paths/names. **True**  
8. All agents should receive every tool. **False**  
9. Structured errors help recovery. **True**  
10. Purpose-built tools can be safer than unrestricted shell access. **True**
