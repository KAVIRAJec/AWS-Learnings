# Amazon Bedrock Knowledge Bases

Fully managed RAG: ingests documents from a data source, parses, chunks, embeds, stores vectors, and serves retrieval (`Retrieve`) or retrieval plus generation (`RetrieveAndGenerate`).

**Key Concepts:**
- **Chunk**: the unit that is embedded, stored, and returned at retrieval; keeps a mapping back to the source document.
- **Retrieve-then-generate**: embed chunks once, embed the query at runtime, fetch top-k similar chunks, pass only those — prompt size depends on the query, not the document size.

```
Ingestion                                              Query time
S3 doc → Parse → Chunk → Embed (Titan/Cohere/Nova) → Vector index
                                                          ▲
User question → Embed → top-k similar chunks ────────────┘
                              │ (hierarchical: child hit → parent returned)
                              ▼
                  Prompt = instructions + retrieved chunks + question  → FM
```

| File | Content |
|---|---|
| `01-Chunking.md` | Default/fixed/hierarchical/semantic/none, custom Lambda chunking, strategy selection |
| `02-Parsing.md` | Default parser vs Bedrock Data Automation vs FM parser |
| `03-Query-Time-Retrieval.md` | numberOfResults, hybrid search, reranking, query decomposition, metadata filters, generation cap |

Pending: vector store options, ingestion/sync, `Retrieve` vs `RetrieveAndGenerate` internals, IAM/encryption.
