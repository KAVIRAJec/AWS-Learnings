## Amazon OpenSearch Service

Amazon OpenSearch Service (formerly Elasticsearch Service) is a managed service for **full-text search, log analytics, and real-time monitoring** — based on the open-source OpenSearch engine (fork of Elasticsearch).

**Key Concepts:**
- **OpenSearch Dashboards**: Built-in visualization tool (fork of Kibana) — create charts, dashboards, and alerting on your data.
- **Index**: A collection of JSON documents — equivalent to a table. Each document has fields that are indexed for fast search.
- **Shard**: An index is split into shards distributed across nodes — enables parallel search and horizontal scaling.
- Supports **full-text search**, structured queries, aggregations, and **anomaly detection** (ML-based).
- Does **not** support SQL natively — uses **OpenSearch DSL** (JSON-based query language). SQL plugin available but limited.
- Deploy in a **VPC** (private) or public endpoint. Supports Multi-AZ with replicas for high availability.

**Deployment Options:**
- **Managed Cluster**: You choose instance types and number of nodes — full control over configuration.
- **OpenSearch Serverless**: Fully managed, auto-scales compute and storage — no cluster configuration needed.

**Ingestion Sources:**
- **Kinesis Data Firehose** → OpenSearch (real-time log delivery).
- **CloudWatch Logs** → subscription filter → OpenSearch.
- **DynamoDB Streams / S3** → Lambda → OpenSearch.
- **Logstash / OpenSearch Ingestion Pipeline** for custom pipelines.
- **Kinesis Data Streams** → Kinesis Data Firehose(Near real-time) → OpenSearch. (or) **Kinesis Data Streams** → Lambda(real-time) → OpenSearch.

**Security:**
- Encryption at rest (KMS) and in transit (TLS).
- **Fine-grained access control**: Index-level, document-level, and field-level permissions.
- Integrates with **Cognito** for dashboard authentication.

**Use cases:** Application & infrastructure log analytics, full-text product/document search, SIEM (security event monitoring), clickstream analytics.
