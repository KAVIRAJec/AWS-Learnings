## Amazon MSK (Managed Streaming for Apache Kafka)

Amazon MSK is a fully managed service for running **Apache Kafka** clusters on AWS — removes the operational burden of provisioning, configuring, and managing Kafka brokers and ZooKeeper nodes. **Alternative to Kinesis Data Streams** for real-time data streaming and analytics, especially if you want to use Kafka's open-source ecosystem or migrate existing Kafka workloads to AWS.

**Key Concepts:**
- **Broker**: Kafka server that stores and serves messages. MSK manages provisioning, patching, and failure recovery of brokers.
- **Topic**: A named channel where producers publish messages and consumers read from. Topics are split into **partitions** for parallelism.
- **Partition**: Unit of parallelism — each partition is an ordered, immutable sequence of messages. More partitions = higher throughput.
- **Producer**: Publishes messages to a topic.
- **Consumer / Consumer Group**: Reads messages from topics. Each partition is consumed by one consumer in a group at a time.
- Data stored on **EBS volumes** — persistent, durable storage (unlike Kinesis which manages its own storage).
- Default message size: **1 MB**. Configurable up to **10 MB** per message.
- Data retention: Configurable — retained as long as disk allows (unlike Kinesis which has a max of 365 days).

**Deployment Options:**
- **MSK Provisioned**: You choose broker instance type and number of brokers per AZ. Multi-AZ by default (3 AZs).
- **MSK Serverless**: Auto-scales compute and storage — no broker management. Pay per usage.
- **MSK Connect**: Run managed Kafka Connect connectors (e.g., S3 Sink, Debezium for CDC) without managing infrastructure.

**Security:**
- Encryption in transit (TLS) or PLAINTEXT and at rest (KMS).
- Authentication: **mTLS**, **SASL/SCRAM**, or **IAM** (for MSK Serverless).
- VPC-based — brokers are in your VPC, not publicly accessible by default.

**Consumers / Integrations:**
- Lambda (event source mapping from MSK).
- Glue Streaming ETL, Kinesis Data Analytics (Flink).
- Any Kafka-compatible consumer (custom apps, Spark, Flink).

**Kinesis vs MSK:**
| | Kinesis Data Streams | Amazon MSK |
|-|---------------------|-----------|
| Protocol | AWS proprietary | Apache Kafka (open standard) |
| Message size | 1 MB | 1MB(deafult), Up to 10 MB |
| Retention | Up to 365 days | Disk-based (no hard limit) |
| Scaling unit | Shards | Partitions / brokers |
| Migration | AWS-native only | Drop-in for self-managed Kafka |
| Serverless | Yes (on-demand) | Yes (MSK Serverless) |

**Use cases:** Event streaming, real-time data pipelines, log aggregation, CDC (Change Data Capture), migrating self-managed Kafka to AWS.
