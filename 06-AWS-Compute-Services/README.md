## AWS Compute Services

| # | Service | Description |
|---|---------|-------------|
| 01 | [EC2](./01-EC2/README.md) | Virtual servers in the cloud |
| 02 | [Lambda](./02-Lambda/README.md) | Serverless function execution |
| 03 | [ECS](./03.ECS/README.md) | Managed Docker container orchestration |
| 04 | [ECR](./04.ECR/README.md) | Managed Docker container image registry |
| 05 | [Lightsail](./05-Lightsail/README.md) | Simplified VPS for small apps |
| 06 | [Batch](./06-Batch/README.md) | Fully managed batch processing |
| 07 | [EKS](./07-EKS/README.md) | Managed Kubernetes service |
| 08 | [Fargate](./08-Fargate/README.md) | Serverless compute engine for ECS/EKS |
| 09 | [Elastic Beanstalk](./09-Elastic-Beanstalk/README.md) | PaaS for deploying web applications |
| 10 | [App Runner](./10-App-Runner/README.md) | Fully managed container/code deployment |
| 11 | [App2Container](./11-App2Container/README.md) | Containerize existing Java/.NET apps |
| 12 | [API Gateway](./12-API-Gateway/README.md) | Managed REST, HTTP, and WebSocket APIs |
| 13 | [Step Functions](./13-Step-Functions/README.md) | Serverless workflow orchestration |
| 14 | [AppSync](./14-AppSync/README.md) | Managed GraphQL API — real-time subscriptions, multi-source data aggregation |

---

### AWS Serverless Services

Serverless means no servers to provision or manage — AWS handles infrastructure, scaling, and availability automatically. You pay only for what you use.

**Compute:**
- [Lambda](./02-Lambda/README.md) — Run code in response to events.
- [Fargate](./08-Fargate/README.md) — Run containers without managing EC2 nodes.
- [App Runner](./10-App-Runner/README.md) — Deploy web apps/APIs directly from code or image.

**API & Orchestration:**
- [API Gateway](./12-API-Gateway/README.md) — Expose serverless APIs.
- [Step Functions](./13-Step-Functions/README.md) — Orchestrate serverless workflows.

**Storage & Database:**
- [S3](../07-AWS-Storage-Services/01-S3/README.md) — Object storage.
- [DynamoDB](../08-AWS-Database-Services/README.md) — Serverless NoSQL database (on-demand mode).
- [Aurora Serverless](../08-AWS-Database-Services/README.md) — Auto-scaling relational database.
- [EFS](../07-AWS-Storage-Services/03-EFS/README.md) — Serverless shared file storage.

**Messaging & Streaming:**
- [SNS](../10-AWS-Monitoring-and-Management/08-SNS/README.md) — Pub/sub notifications.
- [SQS](../10-AWS-Monitoring-and-Management/09-SQS/README.md) — Managed message queuing.
- [Kinesis Data Firehose](../13-Data-Analytics-Services/04-Kinesis/README.md) — Managed streaming delivery.

**Analytics:**
- [Athena](../13-Data-Analytics-Services/01-Athena/README.md) — Serverless SQL on S3.
- [Glue](../13-Data-Analytics-Services/02-Glue/README.md) — Serverless ETL.

---