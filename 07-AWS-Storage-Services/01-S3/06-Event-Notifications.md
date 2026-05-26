## S3 Event Notifications

S3 can send notifications whenever certain events occur in a bucket — triggering workflows, processing pipelines, or alerts automatically.

**Supported events:** `s3:ObjectCreated:*`, `s3:ObjectRemoved:*`, `s3:ObjectRestore:*`, `s3:Replication:*`, and more.

---

### Direct Notification Destinations

S3 can send events directly to three AWS services. Each requires a **resource-based policy** on the destination granting S3 permission to publish.

| Destination | Policy needed | Use case |
|---|---|---|
| **SNS** | SNS Resource Policy | Fan-out notifications — email, SMS, multiple subscribers |
| **SQS** | SQS Resource Policy | Queue events for async processing by consumers |
| **Lambda** | Lambda Resource Policy | Serverless real-time processing of each event |

---

### Amazon EventBridge

Every S3 event is **automatically sent to EventBridge** — regardless of whether direct notifications are configured.

- EventBridge receives **all event types** from S3.
- From EventBridge, route events to **18+ AWS services** as targets: Step Functions, Kinesis Streams, Firehose, SNS, SQS, Lambda, EC2, ECS, and more.
- **Advanced filtering**: Filter by object metadata, object size, object name patterns, prefix, suffix — more powerful than direct S3 notification filters.
- **Multiple targets**: One event can trigger multiple destinations simultaneously.
- **Event archiving and replay**: Store events and replay them for debugging or reprocessing.

```
S3 Event (e.g., ObjectCreated)
        │
        ├──▶ Direct: SNS / SQS / Lambda  (simple, limited filtering)
        │
        └──▶ EventBridge  (all events, advanced filtering, 18+ targets)
                  │
                  ├──▶ Step Functions  (start a workflow)
                  ├──▶ Kinesis Streams (stream for analytics)
                  ├──▶ Firehose        (deliver to S3/Redshift/OpenSearch)
                  └──▶ Lambda          (custom processing)
```

> **Prefer EventBridge** when you need advanced filtering, multiple targets, or event replay. Use direct notifications for simple single-destination setups.
