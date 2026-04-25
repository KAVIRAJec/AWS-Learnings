## Amazon OpenSearch Service

Amazon OpenSearch Service (formerly Elasticsearch Service) is a managed service for **search, log analytics, and real-time application monitoring**.

**Key Concepts:**
- Based on **OpenSearch** (open-source fork of Elasticsearch) + **OpenSearch Dashboards** (fork of Kibana) for visualization.
- Deploy in a VPC or public endpoint. Multi-AZ supported with replicas.
- Integrates with **Kinesis Firehose, CloudWatch Logs, and IoT** for log ingestion.
- **Index**: A collection of documents (similar to a table in a DB).
- Supports full-text search, structured queries, aggregations, and anomaly detection.
- Does **not** support SQL natively (unlike Athena/Redshift) — uses OpenSearch DSL.

**Use cases:** Application log analytics, full-text search, security event monitoring (SIEM), clickstream analysis.
