## Amazon SNS (Simple Notification Service)

Amazon SNS is a fully managed **pub/sub messaging** service that pushes notifications from publishers to multiple subscribers simultaneously.

**Key Concepts:**
- **Topic**: A logical channel where publishers send messages and subscribers receive them. Each topic has a unique ARN.
- **Publisher**: Sends a message to a topic (e.g., CloudWatch Alarm, S3 event, EC2, Lambda, or your application).
- **Subscriber**: Receives messages from a topic. Supported endpoints: **SQS, Lambda, Kinesis Data Firehose(not KDS), HTTP/S, Email, SMS, mobile push (APNS, FCM)**.
- Messages are **not persisted** — delivered immediately. Undelivered messages are lost (use SQS subscriber for durability).
- Up to **12.5 million subscriptions** per topic, **100,000 topics** per account.
- Publisher can send messages to only one topic at a time, but a topic can have multiple publishers. 

**Publishing:**
- Topic Publish(using SDK) → Message is sent to all subscribers.
- Direct Publish(for mobile push) → Send to specific endpoint (e.g., APNS token).

**Topic Types:**
- **Standard Topic**: Best-effort ordering, at-least-once delivery, high throughput.
- **FIFO Topic**: Strict ordering, exactly-once delivery. Only SQS Standard/FIFO queues can subscribe. Topic name must end with `.fifo`.

**Message Filtering:**
- Each subscriber can attach a **filter policy** (JSON) to receive only messages matching specific attributes — avoids receiving all messages from a topic.

**Fan-out Pattern:**
- Publish once to SNS → delivers to multiple SQS queues, Lambda functions, or HTTP endpoints in parallel.
- Common use case: S3 event → SNS → fan out to SQS (processing) + Lambda (alerts) + Email.

**Encryption:**
- In-flight encryption via HTTPS.
- At-rest encryption via SSE with KMS.
- Client side encryption supported by SDKs.

**SNS Access Policies:**
- Similar to S3 bucket policies, control who can publish/subscribe to topics.
- Used for cross-account/cross region access, allowing other AWS accounts/services to publish/subscribe.
