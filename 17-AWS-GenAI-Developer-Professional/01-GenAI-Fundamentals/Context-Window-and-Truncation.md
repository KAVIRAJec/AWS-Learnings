# Context Window and Truncation

A model sees only what fits in its context window (input + output tokens). Anything beyond it is rejected or cut, and the model never sees the dropped content. Remedy is retrieval and chunking: [Knowledge Bases](../02-Amazon-Bedrock/02-Knowledge-Bases/README.md), [Chunking](../02-Amazon-Bedrock/02-Knowledge-Bases/01-Chunking.md).

**Token-budget failure modes:**

| Failure | Cause |
|---|---|
| Overflow | Whole document stuffed into one prompt |
| Context growth | Multi-step workflow appends prior output at each hop |
| Cut response | `maxTokens` too low (truncates the **answer**) |

---

## Diagnosing Truncation and Input Loss

| Symptom | Likely cause | Fix |
|---|---|---|
| Answer cut mid-sentence | `stopReason` = `max_tokens` | Raise `maxTokens`, shorten prompt (Converse `usage` shows token counts) |
| Request rejected | Input over the context window (`ValidationException`) | Retrieval/chunking, not retries |
| Content missing only at peak load | Throttling (`ThrottlingException`, TPM/RPM quotas), or SQS/Lambda payload and batch-size limits | Check each hop's payload, not just the final prompt |
| Cannot see what the model got | No prompt visibility | Enable model invocation logging; compare logged input tokens to the window per step |
| Multi-step prompts keep growing | Appending full prior outputs | Pass chunk IDs/references and re-retrieve |
