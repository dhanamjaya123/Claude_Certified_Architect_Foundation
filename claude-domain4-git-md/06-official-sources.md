# Domain 4 — Official Sources

Technical details in this study pack were cross-checked against current public Claude documentation.

## 1. Prompting best practices
https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/prompt-templates-and-variables

Key topics:
- clear and direct instructions
- explicit output constraints
- few-shot / multishot examples
- XML prompt structure
- long-context prompting
- prompt chaining and self-review

## 2. Structured outputs
https://platform.claude.com/docs/en/build-with-claude/structured-outputs

Key topics:
- JSON outputs using `output_config.format`
- JSON Schema
- strict tool use with `strict: true`
- schema compatibility and limitations
- schema-constrained generation

Important principle:
Structured outputs guarantee schema-conformant structure for supported schemas, but application-level semantic/business validation may still be required.

## 3. Increase output consistency
https://platform.claude.com/docs/en/test-and-evaluate/strengthen-guardrails/increase-consistency

Useful for:
- output formatting
- consistency
- when to prefer Structured Outputs over prompt-only JSON formatting

## 4. Claude API errors
https://docs.anthropic.com/en/api/errors

Useful for:
- API error handling
- request IDs
- long-running requests
- batch-related reliability guidance

## 5. PDF support / batch document processing
https://docs.anthropic.com/en/docs/build-with-claude/pdf-support

Useful for:
- document extraction
- processing many PDFs with Message Batches

> The supplied screenshot defines the exam-study scope and shows Domain 4 as 20%. Product APIs and exact parameter names can change, so use the official certification blueprint as the final source of truth for the exam.
