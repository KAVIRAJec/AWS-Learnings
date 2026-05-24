## Amazon SQS (Simple Queue Service)

Amazon SQS is a fully managed **message queuing** service that decouples and scales distributed systems and microservices.

**Key Concepts:**
- **Queue**: A buffer that stores messages until a consumer processes and deletes them.
- **Producer**: Sends messages to the queue.
- **Consumer**: Polls the queue, processes messages, then deletes them.
- **Message Retention**: 4 days by default, maximum 14 days.
- Unlimited throughput with unlimited number of messages.
- Low latency (<10ms) for message delivery.
- Limitation of 1048 KB(1 MiB) per message size.
**Long Polling**: Consumer waits up to 1 to 20 seconds for a message — reduces empty responses and cost vs short polling. It reduces the number of API requests and improves efficiency by waiting for messages to arrive instead of returning immediately with an empty response when no messages are available.

**Functionality:**
- **Producer**: SendMessage, SendMessageBatch
- **Consumer**: ReceiveMessage, DeleteMessage, ChangeMessageVisibility

**Dead Letter Queue (DLQ)**: Messages that fail processing repeatedly are moved here for inspection.

**Message Visibility Timeout**: 
- Time after a consumer receives a message during which it is invisible to other consumers. If not deleted within this time, it becomes visible again for processing by others if the consumer fails to process it.
- If a message is not processed by a consumer, it becomes visible again after the visibility timeout expires, which makes processing multiple times.
- If consumer processing a message which takes longer than the visibility timeout, the message will become visible again and may be processed by another consumer, leading to potential duplicate processing. To avoid this, you can adjust the visibility timeout to a longer duration or use the ChangeMessageVisibility API to extend it while processing.

**Queue Types:**
- **Standard Queue**: At-least-once delivery, best-effort ordering, **nearly unlimited TPS** (no hard cap — AWS scales it automatically).
  - Also supports batching up to 10 messages per operation (`SendMessageBatch`) — no TPS ceiling even with batching.
- **FIFO Queue**: Exactly-once processing(no duplicates at queue level), strict order preserved. Ordered by message group ID, not across groups. Queue name must end with **.fifo**.
  - **Default throughput**: 300 msg/s (send, receive, or delete operations per second).
  - **With batching** (up to 10 messages per operation): 300 operations × 10 = **3,000 msg/s**.
  - High-throughput mode (optional): up to 9,000 msg/s without batching, 90,000 msg/s with batching.

**Encryption**
- SSE with SQS managed keys (SSE-SQS) for automatic encryption at rest(default).
- Server-side encryption (SSE) with AWS KMS for data at rest.
- In-flight encryption with HTTPS endpoints.
- Client-side encryption with AWS SDKs.

**SQS Access Policies**
- Similar to S3 policies, attached to queues to control permissions for producers and consumers.
- Useful for cross-account access and allowing services to produce or consume from queues.

**Use cases:**
- Using Auto Scaling with SQS: Auto Scaling can be configured to scale EC2 instances based on the number of messages in an SQS queue. This allows your application to automatically adjust its capacity to handle varying workloads, ensuring that you have enough resources to process messages efficiently without over-provisioning. By monitoring the queue length and setting CloudWatch alarms, you can trigger Auto Scaling actions to add or remove instances as needed, optimizing performance and cost.
- Decoupling microservices: SQS allows different components of a microservices architecture to communicate asynchronously, improving scalability and fault tolerance.