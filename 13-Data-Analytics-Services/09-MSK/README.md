## Amazon MSK (Managed Streaming for Apache Kafka)

Amazon MSK is a fully managed service for running **Apache Kafka** clusters on AWS.

**Key Concepts:**
- Apache Kafka is an open-source distributed event streaming platform — MSK removes the operational overhead.
- MSK manages broker provisioning, patching, scaling, and failure recovery.
- Data stored on **EBS volumes** (unlike Kinesis which manages its own storage).
- **MSK Serverless**: Auto-scales capacity without managing broker infrastructure.
- Consumers and producers use standard **Kafka APIs** — no code changes needed when migrating from self-managed Kafka.
- Integrates with Lambda, Glue, Kinesis Data Analytics (Flink), and S3.

**Kinesis vs MSK:**
- Kinesis: AWS-native, simpler, limited message size (1MB), managed retention.
- MSK: Kafka-compatible, higher message size (up to 10MB configurable), more flexibility, more operational complexity.

**Use cases:** Event streaming, log aggregation, real-time data pipelines, migrating self-managed Kafka to AWS.
