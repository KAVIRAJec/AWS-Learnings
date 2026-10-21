# Bedrock Reranker Models

A reranker scores each candidate document against the query and reorders them by relevance. Used after retrieval so the FM gets fewer, better chunks (higher precision, lower token cost and latency). Text only.

> **Mental model:** retrieval is the wide net; the reranker is the editor who reads each candidate next to the question and puts the best first.

Where it sits in the filter → retrieve → rerank pipeline: [Hybrid Search](../../05-Vector-Stores-and-Embeddings/02-Hybrid-Search.md).

**Key Concepts:**
- **Reranker**: model trained to extract relevance signals from (query, document); outputs `relevanceScore`.
- **Override**: reranker results replace the Knowledge Base default ranking.
- **Two ways to use**: direct `Rerank` API on any documents, or inside KB `Retrieve` / `RetrieveAndGenerate`.

---

## Models

| Model | Model ID | Regions (single-region) |
|---|---|---|
| Amazon Rerank 1.0 | `amazon.rerank-v1:0` | ap-northeast-1, ca-central-1, eu-central-1, us-west-2 (**not** us-east-1) |
| Cohere Rerank 3.5 | `cohere.rerank-v3-5:0` | ap-northeast-1, ca-central-1, eu-central-1, us-east-1, us-west-2 |

- Cohere (third-party) needs `aws-marketplace:ViewSubscriptions` and `aws-marketplace:Subscribe` on the role, and model access.
- Rerank models are not usable directly in the console; the console only exposes them in KB query configuration.

---

## Two Ways to Rerank

| | Direct `Rerank` API | Inside KB query |
|---|---|---|
| Endpoint | `bedrock-agent-runtime` | `Retrieve` / `RetrieveAndGenerate` |
| Input | Your own query + documents (inline text or JSON) | KB retrieval results |
| Use when | Documents come from OpenSearch, a database, API, etc. | Content already in a Knowledge Base |
| Config | `rerankingConfiguration` | `vectorSearchConfiguration.rerankingConfiguration` |
| Result count | `numberOfResults` | `numberOfRerankedResults` (1–100) |

### Direct Rerank request/response

| Field | Detail |
|---|---|
| `queries` | Array of **one** `RerankQuery`: `type: TEXT`, `textQuery.text` |
| `sources` | `RerankSource[]`, each `type: INLINE`; document `TEXT` (`textDocument.text`) or `JSON` (`jsonDocument`) |
| `rerankingConfiguration.type` | `BEDROCK_RERANKING_MODEL` |
| `…modelConfiguration.modelArn` | `arn:aws:bedrock:<region>::foundation-model/cohere.rerank-v3-5:0` |
| `…numberOfResults` | How many to return |
| Response `results[]` | `index` (**zero-based** position in `sources`), `relevanceScore`, `document` |
| Pagination | `nextToken` |

```
client = boto3.client('bedrock-agent-runtime')
client.rerank(
  queries=[{'type':'TEXT','textQuery':{'text': question}}],
  sources=[{'type':'INLINE','inlineDocumentSource':
            {'type':'TEXT','textDocument':{'text': chunk}}} for chunk in candidates],
  rerankingConfiguration={'type':'BEDROCK_RERANKING_MODEL',
    'bedrockRerankingConfiguration':{
       'modelConfiguration':{'modelArn': RERANK_ARN},
       'numberOfResults': 5}})
```

### KB-integrated config

| Field | Detail |
|---|---|
| `rerankingConfiguration.type` | `BEDROCK_RERANKING_MODEL` |
| `bedrockRerankingConfiguration.modelConfiguration` | Required; model ARN |
| `numberOfRerankedResults` | 1–100; results returned after reranking |
| `metadataConfiguration.selectionMode` | `ALL` or `SELECTIVE`; metadata fields considered in reranking |

Pair with `numberOfResults` (1–100) on retrieval: set it **higher** than `numberOfRerankedResults` so the reranker has candidates to choose from.

---

## Permissions

| Use case | Caller needs | KB service role needs |
|---|---|---|
| Rerank API alone | `bedrock:Rerank`, `bedrock:InvokeModel` (on rerank model) | n/a |
| In `Retrieve` | `bedrock:Retrieve` | `bedrock:Rerank`, `bedrock:InvokeModel` |
| In `RetrieveAndGenerate` | `bedrock:RetrieveAndGenerate`, `bedrock:Rerank`, `bedrock:InvokeModel` (rerank **and** generation models) | n/a |

> [!WARNING]
> To block reranking you must deny **both** `bedrock:Rerank` and `bedrock:InvokeModel`. A custom KB service role isn't auto-updated by the console; add permissions yourself.

---

## Reranking Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Rerank gives no gain | Candidate set too small or already irrelevant | Raise `numberOfResults`, add filters/hybrid before rerank |
| `AccessDenied` in `Retrieve` with reranker | KB service role missing `Rerank`/`InvokeModel` | Update service role |
| Model not found in us-east-1 | Amazon Rerank 1.0 not offered there | Use Cohere Rerank 3.5 or another Region |
| Non-text content not ranked | Reranking is **text only** | Convert/parse to text first |
| Cohere access error | Marketplace subscription missing | Add `aws-marketplace:*` actions, request model access |

---

## Need → Pick

| Need | Pick |
|---|---|
| Content in Bedrock KB | `rerankingConfiguration` in `Retrieve`/`RetrieveAndGenerate` |
| Content in OpenSearch/DB (not a KB) | Direct `Rerank` API on the candidate list |
| Ranking uses attributes like date or source | `metadataConfiguration` (`ALL` / `SELECTIVE`) |

**Reranker vs Embedding model vs FM-as-judge:**

| | Embedding retrieval | Reranker | FM decides relevance |
|---|---|---|---|
| Looks at | Query vector vs doc vector (computed separately) | Query and document together | Whatever you put in the prompt |
| Speed/cost | Fast, cheap | Moderate, per candidate | Slowest, token-heavy |
| Best at | Recall over large corpus | Ordering a shortlist | Final synthesis, not ranking |
| Weakness | Fine-grained specificity | Needs good candidates | Hallucination risk if given poor context |
