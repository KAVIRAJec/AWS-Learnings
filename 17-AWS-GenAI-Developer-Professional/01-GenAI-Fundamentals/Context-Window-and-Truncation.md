# Context Window and Truncation

Long documents cannot be sent to an FM whole: input plus output must fit the model's context window. The fix is to split documents into chunks at ingestion, embed them, and at query time retrieve only the chunks relevant to the question — so prompt size depends on the query, not the document size.

**Key Concepts:**
- **Context window**: max tokens (input + output) a model handles per request; exceeding it causes truncation or a rejected request, depending on the model/API — the model never sees the dropped content.
- **Chunk**: the unit that is embedded, stored in the vector index, and returned at retrieval. Keeps a mapping back to the source document.
- **Token-budget failure modes**: (1) stuffing everything into one prompt, (2) chaining steps that each append prior output, so context grows per hop, (3) `maxTokens` too low so the *response* is cut off.
- **Retrieve-then-generate**: embed chunks once, embed the query at runtime, fetch top-k similar chunks, pass only those — keeps every request inside the window regardless of document size.

Chunking and retrieval remedies: [Knowledge Bases chunking](../02-Amazon-Bedrock/02-Knowledge-Bases/01-Chunking.md).

---

## Diagnosing Truncation and Input Loss

- **Answer cut mid-sentence**: response `stopReason` is `max_tokens` → raise `maxTokens` or shorten the prompt. (Converse API returns `usage` with input/output token counts.)
- **Request rejected**: input exceeds the model's context window → `ValidationException`; fix with retrieval/chunking, not retries.
- **Content missing only at peak load**: suspect throttling (`ThrottlingException`, tokens-per-minute/requests-per-minute quotas) and async/SQS/Lambda payload or batch-size handling in the workflow rather than the model — check each hop's payload size, not only the final prompt.- **Observability**: enable Bedrock model invocation logging to see the exact prompt the model received; compare logged input tokens against the window per workflow step.
- **Multi-step workflows**: pass retrieved chunk IDs/references between steps and re-retrieve, instead of appending full prior outputs into each next prompt.
