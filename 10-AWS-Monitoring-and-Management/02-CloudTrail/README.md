## AWS CloudTrail

AWS CloudTrail records **API calls and account activity** across your AWS infrastructure for governance, compliance, and auditing.

**Key Concepts:**
- **Event**: A record of an action taken — who made the request, what service, what action, when, and from where.
- **Management Events**: Operations on AWS resources (create, delete, modify). Enabled by default.
- **Data Events**: Object-level activity (S3 GetObject, Lambda invocations). Disabled by default — high volume, extra cost.
- **Insight Events**: Detects unusual API activity (e.g., spike in EC2 TerminateInstances calls).
- **Trail**: Configuration to deliver events to an **S3 bucket** and optionally to **CloudWatch Logs**.
- Logs are encrypted with SSE-S3 by default; can use SSE-KMS.
- Events are retained in CloudTrail for **90 days** by default. For longer retention, send to S3.

**Use cases:** Security auditing, troubleshooting, compliance, detecting unauthorized access.
