# Knowledge Bases — Query-Time Retrieval

Controls applied when querying a knowledge base with `Retrieve` or `RetrieveAndGenerate`; they decide how many and which chunks reach the prompt.

## Controls

- **`numberOfResults`**: default 5 chunks per query. Raising it increases prompt size; with hierarchical chunking it counts child chunks.
- **Search type** (`overrideSearchType`): `HYBRID` (vector + raw text) or `SEMANTIC`. Hybrid only for RDS (Aurora), OpenSearch Serverless, MongoDB stores with a filterable text field — otherwise falls back to semantic.
- **Reranker model**: re-scores retrieved chunks so the best ones survive a small `numberOfResults`.
- **Query decomposition** (`orchestrationConfiguration.queryTransformationConfiguration.type = QUERY_DECOMPOSITION`): splits a multi-part question into sub-queries, each retrieving its own chunks.
- **Metadata filtering**: narrow the candidate chunks (`equals`, `in`, `greaterThan`, `andAll`/`orAll` up to 5 filters per group, one nesting level). Implicit filtering lets an Anthropic Claude model generate the filter from the question + metadata schema.
- **Generation cap**: `inferenceConfig.textInferenceConfig.maxTokens` — too low produces a truncated *answer*, which looks like "incomplete response".
- **API choice**: `Retrieve` returns chunks only (you build the prompt, full control of token budget); `RetrieveAndGenerate` (and `...Stream`) retrieves and invokes the FM in one call.
