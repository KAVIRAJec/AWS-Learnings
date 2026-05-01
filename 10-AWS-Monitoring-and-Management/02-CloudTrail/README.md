## AWS CloudTrail

AWS CloudTrail records **API calls and account activity** across your AWS infrastructure — captures who did what, when, and from where for governance, compliance, and auditing.

- Enabled by default for all AWS accounts — **Event History** retains the last 90 days of management events at no extra cost.
- For longer retention or advanced querying, create a **Trail** to deliver events to S3(query using Athena) or CloudTrail Lake.

---

## Event Types

**Key Concepts:**
- **Event**: A single record of an API call or action — includes who made the request (principal), what service, what action, when, source IP, request/response parameters, and whether it succeeded.
- **Management Events**: Control-plane operations on AWS resources — `CreateBucket`, `RunInstances`, `DeleteUser`, `AttachRolePolicy`. Enabled by default. Read and write events can be filtered separately.
- **Data Events**: Data-plane, object-level activity — `S3:GetObject`, `S3:PutObject`, `Lambda:Invoke`, `DynamoDB:GetItem`. **Disabled by default** — extremely high volume, charged per event. Must explicitly enable per resource or all resources.
- **Insight Events**: Detects **unusual API activity** by analyzing normal baseline patterns and alerting on deviations — e.g., a sudden spike in `TerminateInstances` or `DeleteSecurityGroup` calls. Charged per event analyzed. Delivered to S3 and optionally CloudWatch Events.
- **Network Activity Events**: Records VPC endpoint activity — API calls made through VPC endpoints. Useful for auditing private network access to AWS services.

---

## Trail

A Trail is a configuration that delivers a continuous stream of CloudTrail events to a destination for long-term retention and analysis.

**Key Concepts:**
- **S3 Bucket**: Primary destination — events delivered as compressed JSON log files, organized by account/region/date.
- **CloudWatch Logs**: Optional — stream events to a log group for real-time monitoring and metric filters (e.g., alert on root account login, IAM policy changes).
- **SNS**: Optional — send notifications when new log files are delivered to S3.
- **Single-region Trail**: Records events only in the region where it was created.
- **Multi-region Trail**: Records events from all regions — delivers to a single S3 bucket. **Recommended default** — ensures no region is missed.
- **Global Service Events**: IAM, STS, CloudFront are global services whose events are always recorded in `us-east-1`. Multi-region trails include these automatically; single-region trails must explicitly enable it.

**Log File Integrity Validation:**
- CloudTrail creates a **digest file** every hour referencing all log files and their SHA-256 hashes.
- Digest files are signed with CloudTrail's private key — validate with `aws cloudtrail validate-logs` CLI command.
- Allows you to detect if log files were modified, deleted, or forged after delivery — critical for forensic investigations.

**Encryption:**
- Default: **SSE-S3** (S3-managed keys) — automatic, no extra cost.
- Recommended: **SSE-KMS** — use a customer-managed KMS key for additional access control and audit trail via CloudTrail itself.

---

## Organization Trail

- Created in the **AWS Organizations management account** — automatically applies to all member accounts in the organization.
- Member accounts can see the trail exists but cannot modify or delete it.
- All member account events delivered to a **central S3 bucket** in the management account — simplifies cross-account auditing without configuring per-account trails.

---

## CloudTrail Lake

CloudTrail Lake is a **managed data lake for CloudTrail events** — stores, queries, and analyzes events using SQL without needing to set up Athena or S3.

**Key Concepts:**
- **Event Data Store**: The core resource — stores events for a configurable retention period (default 7 years, minimum 90 days).
- **SQL Queries**: Run SQL queries directly in the console or via API — filter by event name, principal, time range, error code, etc.
- **Multi-account aggregation**: Aggregate events from multiple AWS accounts and regions into a single event data store.
- **Integrations**: Ingest events from non-AWS sources (on-premises, other clouds) using the **CloudTrail Lake integrations** feature.
- Events are stored in an optimized columnar format — queries are faster and cheaper than querying raw JSON from S3 with Athena.

**CloudTrail Lake vs Trail + Athena:**

| | CloudTrail Lake | Trail + S3 + Athena |
|---|---|---|
| **Setup** | Single event data store, no S3 needed | Trail → S3 → Athena table + Glue |
| **Query language** | SQL (built-in) | SQL via Athena |
| **Retention** | Up to 7 years | Unlimited (S3 lifecycle) |
| **Cost** | Per GB ingested + per GB scanned | S3 storage + Athena query cost |
| **Best for** | Simpler setup, long-term retention | Cost-optimized at very high volume |

---

## CloudTrail Insights

- Continuously analyzes **write management events** to establish a normal baseline.
- Raises an **Insight Event** when activity deviates significantly — unusual call volume, error rate spikes, or abnormal patterns.
- Insight events delivered to: S3 bucket, CloudWatch Events (for alerting), and visible in CloudTrail console.
- **Use case**: Detect a misconfigured automation script making thousands of `DeleteObjects` calls, or a compromised credential running unusual IAM operations.

---

## Common Security Patterns

**Root account login alert:**
- CloudTrail → CloudWatch Logs → Metric Filter on `userIdentity.type = Root` → CloudWatch Alarm → SNS notification.

**Unauthorized API call detection:**
- Metric Filter on `errorCode = AccessDenied` or `UnauthorizedOperation` → alarm on threshold breach.

**IAM policy change alert:**
- Metric Filter on events like `PutUserPolicy`, `AttachRolePolicy`, `CreateUser` → SNS alert.

---

## CloudTrail vs CloudWatch

| | CloudTrail | CloudWatch |
|---|---|---|
| **What it captures** | API calls and account activity (who did what) | Metrics, logs, and operational data (how resources are performing) |
| **Primary use** | Auditing, compliance, security investigation | Monitoring, alerting, dashboards |
| **Default retention** | 90 days (Event History) | Varies by log group (default: never expire) |
| **Data source** | AWS control plane and data plane API calls | Application logs, system metrics, custom metrics |

**Use cases:** Security auditing, detecting unauthorized API calls, compliance evidence, investigating incidents, tracking IAM and configuration changes across accounts.
