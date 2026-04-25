## Amazon MQ

Amazon MQ is a managed **message broker** service that supports industry-standard protocols — designed for migrating existing on-premises messaging systems to AWS without rewriting application code.

**Supported Protocols:** MQTT, AMQP, STOMP, OpenWire, WebSocket.
**Supported Brokers:** **Apache ActiveMQ** and **RabbitMQ**.

**Key Concepts:**
- Unlike SQS/SNS (AWS-native, proprietary APIs), Amazon MQ uses **open standard protocols** — drop-in replacement for on-premises brokers.
- Runs on dedicated instances (not serverless) — you choose instance size. Can configure Multi-AZ for high availability.
- Supports both **queues** (point-to-point) and **topics** (pub/sub) within the same broker.
- **High Availability**: Deploy in Multi-AZ with failover — active/standby broker setup with shared EFS storage.
- Deployed inside a **VPC** — not publicly accessible by default.

**Amazon MQ vs SQS/SNS:**
- Migrating existing apps with AMQP/MQTT → use **Amazon MQ**.
- Building new cloud-native apps → use **SQS/SNS** (serverless, more scalable, tighter AWS integration).
