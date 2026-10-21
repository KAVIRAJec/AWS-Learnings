# Bedrock Inference: Caching, Routing, Throughput

Features that change how model calls are served: reuse of processed prompt prefixes, automatic model selection, and capacity options.

| File | Content |
|---|---|
| `01-Prompt-Caching.md` | Implicit vs explicit caching, checkpoints, TTL, billing fields, troubleshooting |
| `02-Intelligent-Prompt-Routing.md` | Default vs configured routers, fallback model, quality-difference criteria |

**Pending:** Provisioned Throughput, Cross-Region Inference, Batch Inference, latency-optimized models, quotas and throttling.

## Need → Pick

| Need | Pick |
|---|---|
| Cheaper/faster repeated long prefix | [Prompt caching](01-Prompt-Caching.md) |
| Auto-choose cheap vs strong model | [Intelligent Prompt Routing](02-Intelligent-Prompt-Routing.md) |
| Cache whole responses for similar questions | App-built semantic cache (not a Bedrock feature) |
