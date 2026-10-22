# AIP-C01 Coverage Tracker

AWS Certified Generative AI Developer – Professional. Source: [official exam guide](https://docs.aws.amazon.com/aws-certification/latest/ai-professional-01/ai-professional-01.html).

**Status legend:** `[ ]` not started · `[~]` partly covered · `[x]` covered in depth
**Notes column** = file where the content lives (relative to this folder). Tick a task only when all its skills are `[x]`.

| Domain | Weight |
|---|---|
| 1. FM Integration, Data Management, Compliance | 31% |
| 2. Implementation and Integration | 26% |
| 3. AI Safety, Security, Governance | 20% |
| 4. Operational Efficiency and Optimization | 12% |
| 5. Testing, Validation, Troubleshooting | 11% |

Notes are organized **by service** (one folder per service, like the SAA folders), not by domain. A skill below may point to several service folders.

| Folder | Covers |
|---|---|
| `01-GenAI-Fundamentals/` | Tokens, context window, truncation, inference params, RAG vs fine-tuning |
| `02-Amazon-Bedrock/` | `01-Core-and-APIs`, `02-Knowledge-Bases`, `03-Agents-and-AgentCore`, `04-Guardrails`, `05-Prompt-Management-and-Flows`, `06-Inference-Throughput-and-Routing`, `07-Model-Customization`, `08-Data-Automation`, `09-Model-Evaluation` |
| `03-Agent-Frameworks-and-MCP/` | Strands Agents, AWS Agent Squad, Model Context Protocol |
| `04-SageMaker-for-GenAI/` | JumpStart, endpoints, inference components, Model Registry, Clarify, model cards |
| `05-Vector-Stores-and-Embeddings/` | OpenSearch, Aurora pgvector, other stores, Titan/Cohere embeddings |
| `06-Amazon-Q-and-Kendra/` | Q Developer, Q Business, Kendra |
| `07-Supporting-AI-Services/` | Comprehend, Transcribe, Textract, Rekognition as pre/post-processing |
| `08-Integration-and-Orchestration/` | Step Functions, API Gateway, Lambda, EventBridge, SQS, AppConfig, CI/CD and gateway patterns for GenAI |
| `09-Security-Governance-Responsible-AI/` | VPC endpoints, PII, Lake Formation, lineage, model cards, responsible AI |
| `10-Observability-and-Cost-Optimization/` | CloudWatch/X-Ray for GenAI, invocation logs, token cost, caching |
| `11-Testing-and-Troubleshooting/` | Evaluation strategy, regression/quality gates, troubleshooting playbooks |

Folders with only a `.gitkeep` have no content yet.

---

## Domain 1 — FM Integration, Data Management, Compliance (31%)

### [ ] 1.1 Analyze requirements and design GenAI solutions
- [ ] 1.1.1 Architecture design: FM, integration pattern, deployment strategy
- [ ] 1.1.2 Bedrock proof-of-concept to validate feasibility
- [ ] 1.1.3 Well-Architected Framework + Generative AI Lens

### [ ] 1.2 Select and configure FMs
- [ ] 1.2.1 FM selection: benchmarks, capabilities, limitations
- [ ] 1.2.2 Dynamic model/provider switching without code change (Lambda, API Gateway, AppConfig)
- [ ] 1.2.3 Resilience: Step Functions circuit breaker, Cross-Region Inference, graceful degradation
- [ ] 1.2.4 Customization lifecycle: SageMaker fine-tuned models, LoRA/adapters, Model Registry, rollback, retirement

### [ ] 1.3 Data validation and processing pipelines for FMs
- [ ] 1.3.1 Data quality validation (Glue Data Quality, Data Wrangler, Lambda, CloudWatch)
- [~] 1.3.2 Multimodal processing (text/image/audio/tabular; Transcribe, SageMaker Processing) → [parsers](02-Amazon-Bedrock/02-Knowledge-Bases/02-Parsing.md)
- [ ] 1.3.3 Model-specific input formatting (Bedrock JSON, Converse API, conversation format)
- [ ] 1.3.4 Input quality enhancement (Comprehend entities, Lambda normalization)

### [ ] 1.4 Vector store solutions
- [~] 1.4.1 Vector DB architectures (Bedrock KB, OpenSearch, RDS/Aurora, DynamoDB) → [OpenSearch Serverless](05-Vector-Stores-and-Embeddings/01-OpenSearch-Serverless-Vector-Search.md), [KB store options](05-Vector-Stores-and-Embeddings/03-KB-Vector-Store-Options.md); DynamoDB pattern pending
- [~] 1.4.2 Metadata frameworks for search precision → [KB metadata filtering](02-Amazon-Bedrock/02-Knowledge-Bases/03-Query-Time-Retrieval.md), [filter placement](05-Vector-Stores-and-Embeddings/01-OpenSearch-Serverless-Vector-Search.md); S3 metadata/tagging patterns pending
- [~] 1.4.3 High-performance vector stores (sharding, multi-index, hierarchical indexing) → [sharding, memory, HNSW](05-Vector-Stores-and-Embeddings/04-OpenSearch-Performance-Tuning.md); multi-index/hierarchical pending
- [ ] 1.4.4 Integration with document systems, wikis, knowledge sources
- [ ] 1.4.5 Vector store maintenance (incremental update, change detection, sync, refresh)

### [ ] 1.5 Retrieval mechanisms for FM augmentation
- [x] 1.5.1 Chunking strategies (fixed, hierarchical, semantic, custom Lambda) → [chunking](02-Amazon-Bedrock/02-Knowledge-Bases/01-Chunking.md)
- [ ] 1.5.2 Embedding models (Titan, dimensionality, batch generation)
- [x] 1.5.3 Vector search (OpenSearch, Aurora pgvector, Bedrock KB managed stores) → [OpenSearch](05-Vector-Stores-and-Embeddings/01-OpenSearch-Serverless-Vector-Search.md), [store options](05-Vector-Stores-and-Embeddings/03-KB-Vector-Store-Options.md), [Aurora hybrid](05-Vector-Stores-and-Embeddings/02-Hybrid-Search.md)
- [x] 1.5.4 Hybrid search + rerankers → [Hybrid Search](05-Vector-Stores-and-Embeddings/02-Hybrid-Search.md), [Reranking](02-Amazon-Bedrock/02-Knowledge-Bases/04-Reranking.md)
- [~] 1.5.5 Query expansion / decomposition / transformation → [decomposition](02-Amazon-Bedrock/02-Knowledge-Bases/03-Query-Time-Retrieval.md)
- [ ] 1.5.6 Access mechanisms: function calling, MCP clients for retrieval

### [ ] 1.6 Prompt engineering and governance
- [~] 1.6.1 Instruction frameworks (Prompt Management, role definition, templates) → [Prompt Management](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/01-Prompt-Management.md); Guardrails pending
- [ ] 1.6.2 Interactive systems: clarification workflows, intent recognition, conversation history in DynamoDB
- [~] 1.6.3 Prompt management + governance (versions, approvals, S3 repos, CloudTrail) → [governance pattern](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/01-Prompt-Management.md); Step Functions approval pattern pending
- [ ] 1.6.4 Prompt QA: output verification, edge-case testing, regression
- [ ] 1.6.5 Advanced prompting: structured input, output format, CoT, feedback loops
- [x] 1.6.6 Bedrock Prompt Flows: chains, conditional branching, reusable components → [Flows](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/02-Prompt-Flows.md), [nodes](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/03-Flow-Nodes.md)

---

## Domain 2 — Implementation and Integration (26%)

### [ ] 2.1 Agentic AI solutions and tool integrations
- [ ] 2.1.1 Agents with memory/state: Strands Agents, AWS Agent Squad, MCP
- [ ] 2.1.2 Structured reasoning: ReAct + CoT via Step Functions
- [ ] 2.1.3 Safeguarded workflows: stopping conditions, timeouts, IAM boundaries, circuit breakers
- [ ] 2.1.4 Model coordination: specialized FMs, ensembles, model selection frameworks
- [ ] 2.1.5 Human-in-the-loop: Step Functions approval, API Gateway feedback
- [ ] 2.1.6 Tool integrations: Strands API, function definitions, Lambda validation/error handling
- [ ] 2.1.7 MCP servers: Lambda (stateless) vs ECS (complex), MCP client libraries

### [ ] 2.2 Model deployment strategies
- [ ] 2.2.1 Lambda on-demand vs Bedrock Provisioned Throughput vs SageMaker endpoints
- [ ] 2.2.2 LLM-specific deployment: containers, GPU, memory, token capacity, model loading
- [ ] 2.2.3 Optimized deployment: smaller models, API-based model cascading

### [ ] 2.3 Enterprise integration architectures
- [ ] 2.3.1 Enterprise connectivity: legacy APIs, event-driven, data sync
- [ ] 2.3.2 Integrated AI: API Gateway microservices, Lambda webhooks, EventBridge
- [ ] 2.3.3 Secure access: identity federation, RBAC, least-privilege FM access
- [ ] 2.3.4 Cross-environment/data residency: Outposts, Wavelength, secure routing
- [ ] 2.3.5 CI/CD + GenAI gateway: CodePipeline/CodeBuild, security scans, rollback, central abstraction

### [ ] 2.4 FM API integrations
- [ ] 2.4.1 Sync/async interaction: Bedrock APIs, SDKs, SQS, API Gateway validation
- [ ] 2.4.2 Streaming: Bedrock streaming APIs, WebSockets, SSE, chunked transfer
- [ ] 2.4.3 Resilience: exponential backoff, rate limiting, fallbacks, X-Ray
- [~] 2.4.4 Model routing: static, Step Functions content-based, metric-based, API Gateway transforms → [prompt routing](02-Amazon-Bedrock/06-Inference-Throughput-and-Routing/02-Intelligent-Prompt-Routing.md)

### [ ] 2.5 Application integration patterns and dev tools
- [ ] 2.5.1 FM API interfaces: streaming via API Gateway, token limits, timeout retries
- [~] 2.5.2 Accessible interfaces: Amplify, OpenAPI, Prompt Flows → [Flows](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/02-Prompt-Flows.md); Amplify/OpenAPI pending
- [ ] 2.5.3 Business system enhancements: Lambda CRM, Step Functions doc processing, Bedrock Data Automation
- [ ] 2.5.4 Developer productivity: Amazon Q Developer
- [ ] 2.5.5 Advanced apps: Strands/Agent Squad, Step Functions agent patterns, prompt chaining
- [ ] 2.5.6 Troubleshooting efficiency: Logs Insights, X-Ray, Q Developer

---

## Domain 3 — AI Safety, Security, Governance (20%)

### [ ] 3.1 Input and output safety controls
- [ ] 3.1.1 Input content safety: Guardrails, custom moderation workflows
- [ ] 3.1.2 Output content safety: Guardrails, toxicity evals, text-to-SQL determinism
- [ ] 3.1.3 Hallucination reduction: KB grounding, confidence scoring, JSON Schema outputs
- [ ] 3.1.4 Defense in depth: Comprehend pre-filter, Guardrails, Lambda post-validation, API Gateway filtering
- [ ] 3.1.5 Threat detection: prompt injection, jailbreak, sanitization, adversarial testing

### [ ] 3.2 Data security and privacy controls
- [ ] 3.2.1 Protected environments: VPC endpoints, IAM, Lake Formation, CloudWatch
- [ ] 3.2.2 PII detection: Comprehend, Macie, Bedrock data privacy, S3 lifecycle retention
- [ ] 3.2.3 Privacy-preserving utility: masking, anonymization, Guardrails

### [ ] 3.3 AI governance and compliance
- [ ] 3.3.1 Compliance frameworks: SageMaker model cards, Glue lineage, decision logs
- [ ] 3.3.2 Data source tracking: Glue Data Catalog, metadata attribution, CloudTrail
- [ ] 3.3.3 Organizational governance frameworks
- [ ] 3.3.4 Continuous monitoring: misuse/drift/bias detection, alerting, token-level redaction

### [ ] 3.4 Responsible AI
- [ ] 3.4.1 Transparency: reasoning display, confidence metrics, source attribution, agent tracing
- [~] 3.4.2 Fairness: metrics, Prompt Management A/B testing, LLM-as-a-judge → [variants](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/01-Prompt-Management.md); fairness metrics and LLM-judge pending
- [ ] 3.4.3 Policy compliance: Guardrails, model cards, automated compliance checks

---

## Domain 4 — Operational Efficiency and Optimization (12%)

### [ ] 4.1 Cost optimization and resource efficiency
- [ ] 4.1.1 Token efficiency: estimation, context optimization, compression, pruning
- [~] 4.1.2 Cost-effective model selection: tiering by complexity, price-to-performance → [prompt routing](02-Amazon-Bedrock/06-Inference-Throughput-and-Routing/02-Intelligent-Prompt-Routing.md)
- [ ] 4.1.3 Throughput systems: batching, capacity planning, Provisioned Throughput
- [~] 4.1.4 Caching: semantic, fingerprinting, edge, prompt caching → [prompt caching](02-Amazon-Bedrock/06-Inference-Throughput-and-Routing/01-Prompt-Caching.md); semantic/edge pending

### [ ] 4.2 Application performance
- [ ] 4.2.1 Latency-cost tradeoffs: pre-computation, latency-optimized models, parallelism, streaming
- [~] 4.2.2 Retrieval performance: index optimization, query preprocessing, hybrid scoring → [hybrid scoring](05-Vector-Stores-and-Embeddings/02-Hybrid-Search.md), [filter placement](05-Vector-Stores-and-Embeddings/01-OpenSearch-Serverless-Vector-Search.md); index tuning pending; [HNSW tuning](05-Vector-Stores-and-Embeddings/04-OpenSearch-Performance-Tuning.md)
- [ ] 4.2.3 Throughput optimization: token processing, batch inference, concurrency
- [ ] 4.2.4 Inference parameters: temperature, top-k, top-p, A/B testing
- [ ] 4.2.5 Resource allocation: token capacity planning, GenAI auto-scaling
- [ ] 4.2.6 Workflow optimization: API profiling, vector query optimization, LLM latency reduction

### [ ] 4.3 Monitoring systems
- [ ] 4.3.1 Holistic observability: metrics, tracing, FM interaction tracing, dashboards
- [ ] 4.3.2 GenAI monitoring: token usage, hallucination rate, invocation logs, anomaly detection
- [ ] 4.3.3 Integrated observability: compliance monitoring, audit, behavior tracking
- [ ] 4.3.4 Tool performance: call patterns, multi-agent coordination tracking
- [~] 4.3.5 Vector store operations: monitoring, index optimization, data quality → [k-NN metrics, hot shards](05-Vector-Stores-and-Embeddings/04-OpenSearch-Performance-Tuning.md); automated index routines and data quality pending
- [ ] 4.3.6 FM troubleshooting frameworks: golden datasets, output diffing, reasoning path tracing

---

## Domain 5 — Testing, Validation, Troubleshooting (11%)

### [ ] 5.1 Evaluation systems
- [ ] 5.1.1 Assessment metrics: relevance, factual accuracy, consistency, fluency
- [ ] 5.1.2 Bedrock Model Evaluations, A/B + canary, cost-performance analysis
- [ ] 5.1.3 User-centered evaluation: feedback, ratings, annotation workflows
- [ ] 5.1.4 Continuous QA: regression testing, automated quality gates
- [ ] 5.1.5 RAG evaluation, LLM-as-a-Judge, human feedback
- [ ] 5.1.6 Retrieval quality testing: relevance scoring, context matching, latency
- [ ] 5.1.7 Agent evaluation: task completion, tool usage, reasoning quality
- [ ] 5.1.8 Reporting to stakeholders
- [ ] 5.1.9 Deployment validation: synthetic workflows, hallucination/semantic drift checks

### [ ] 5.2 Troubleshoot GenAI applications
- [~] 5.2.1 Content handling: context window overflow, truncation, dynamic chunking → [diagnosing truncation](01-GenAI-Fundamentals/Context-Window-and-Truncation.md)
- [ ] 5.2.2 FM integration issues: error logging, request validation, response analysis
- [ ] 5.2.3 Prompt problems: testing frameworks, version comparison
- [~] 5.2.4 Retrieval issues: embedding diagnostics, drift, chunking remediation → [vector search symptoms](05-Vector-Stores-and-Embeddings/04-OpenSearch-Performance-Tuning.md)
- [ ] 5.2.5 Prompt maintenance: template testing, schema validation, prompt observability

---

## Cross-Cutting Topic Backlog

Services/concepts spanning many skills. Cover as dedicated notes, then link from the skills above.

- [ ] Amazon Bedrock core: model catalog, InvokeModel vs Converse API, inference params, quotas/throttling
- [~] Bedrock Knowledge Bases (ingestion, sync, parsing, chunking, retrieval APIs) — chunking/parsing/query config done; sync, stores, Retrieve vs RetrieveAndGenerate internals pending
- [ ] Bedrock Agents (action groups, Lambda, return of control, tracing, memory)
- [ ] Bedrock AgentCore
- [ ] Bedrock Guardrails (all policy types, ApplyGuardrail API, contextual grounding)
- [x] Bedrock Prompt Management / Prompt Flows / prompt caching / Intelligent Prompt Routing → [05-Prompt-Management-and-Flows](02-Amazon-Bedrock/05-Prompt-Management-and-Flows/README.md), [06-Inference-Throughput-and-Routing](02-Amazon-Bedrock/06-Inference-Throughput-and-Routing/README.md)
- [ ] Bedrock Provisioned Throughput, Cross-Region Inference, Batch Inference
- [ ] Bedrock Model Customization (fine-tuning, continued pretraining, distillation, Custom Model Import)
- [ ] Bedrock Data Automation
- [ ] Bedrock Model Evaluation (automatic, human, LLM-as-judge, RAG eval)
- [ ] Strands Agents, AWS Agent Squad, MCP
- [ ] SageMaker for GenAI (JumpStart, endpoints, inference components, Model Registry, Clarify, model cards)
- [x] OpenSearch vector engine / Aurora pgvector / other vector stores → [05-Vector-Stores-and-Embeddings](05-Vector-Stores-and-Embeddings/README.md)
- [ ] Titan and other embedding models
- [ ] Amazon Q (Developer, Business) and Amazon Kendra
- [ ] Comprehend, Transcribe, Textract, Rekognition (pre/post-processing services)
- [ ] Responsible AI concepts (bias, explainability, transparency)
- [ ] GenAI fundamentals (transformers, tokens, context window, temperature/top-k/top-p, RAG vs fine-tuning)
