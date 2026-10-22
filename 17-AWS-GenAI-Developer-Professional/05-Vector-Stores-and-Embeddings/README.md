# Vector Stores and Search for FM Augmentation

Where embeddings live and how they are searched and ranked so retrieved chunks give the FM precise context. The ingest/query pipeline diagram is in [Knowledge Bases](../02-Amazon-Bedrock/02-Knowledge-Bases/README.md).

| File | Content |
|---|---|
| `01-OpenSearch-Serverless-Vector-Search.md` | Collection generations, k-NN mapping, filter placement, Managed vs Serverless, neural search |
| `02-Hybrid-Search.md` | Why keyword and vector fail alone, filter → retrieve → rerank pipeline, OpenSearch hybrid scoring |
| `03-KB-Vector-Store-Options.md` | Store comparison, field mappings, Aurora setup, dimensions, binary vectors |
| `04-OpenSearch-Performance-Tuning.md` | HNSW parameters, memory sizing, sharding and hot shards, k-NN metrics, common non-fixes |

Reranking: [04-Reranking.md](../02-Amazon-Bedrock/02-Knowledge-Bases/04-Reranking.md).

**Pending:** embedding model deep dive (Titan V2, Cohere, batching), multi-index and hierarchical indexing (1.4.3), data maintenance and sync (1.4.5), index-optimization routines (4.3.5).
