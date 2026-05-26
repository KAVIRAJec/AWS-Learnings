## S3 Storage Lens

S3 Storage Lens provides **organization-wide visibility** into S3 storage usage and activity — helps you understand, analyze, and optimize S3 across your entire AWS Organization.

- Aggregates data at the **organization, account, region, bucket, or prefix** level.
- Discovers anomalies, identifies trends, and surfaces cost optimization recommendations.
- Creates a dashboard with metrics and insights. Can export daily metrics as a `.csv` file to an S3 bucket.
- **Default dashboard**: Pre-configured, cannot be modified or deleted. Always available.
- **Custom dashboards**: Create with specific metrics, scopes, and filters based on your needs.

---

### Metric Categories

**Storage Metrics**
- Total storage used, number of objects, storage class distribution.
- Identify fast-growing buckets or objects that are no longer accessed.

**Cost Optimization Metrics**
- Estimated cost based on storage and request activity.
- Identify incomplete multipart uploads and noncurrent object versions that can be moved to a cheaper class or deleted.

**Data Protection Metrics**
- Percentage of objects with versioning enabled, replication status, encryption status.
- Identify unencrypted objects or objects not following data protection policies.

**Access Management Metrics**
- Insights into bucket ownership, public access settings, ACLs, and bucket policies.
- Identify publicly accessible buckets.

**S3 Event Metrics**
- Insights into event notifications — number of events sent to SNS, SQS, Lambda.
- Identify which events are triggered and how frequently.

**Performance Metrics**
- Insights into Transfer Acceleration usage, S3 Select, and S3 Inventory.
- Identify which buckets have Transfer Acceleration enabled.

**Activity Metrics**
- Object-level operation insights — PUT, GET, DELETE, LIST, downloads.
- Identify which objects or prefixes are accessed most frequently.

**Detailed Status Code Metrics**
- Breakdown of request status codes — success (2xx) and errors (4xx, 5xx).
- Identify which requests are failing and diagnose root causes.

---

### Free vs Advanced Metrics

| | Free Metrics | Advanced Metrics |
|---|---|---|
| **Cost** | Free for all customers | Paid |
| **Number of metrics** | ~28 metrics | All free metrics + Activity, Advanced Cost Optimization, Advanced Data Protection, Detailed Status Codes |
| **Data retention** | 14 days | 15 months |
| **Prefix-level metrics** | No | Yes |
| **CloudWatch export** | No | Yes (no additional cost) |
