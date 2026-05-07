## AWS AppFlow

**AWS AppFlow** is a fully managed integration service that securely transfers data between **SaaS applications** (Salesforce, SAP, Slack, Google Analytics, Zendesk, etc.) and **AWS services** (S3, Redshift, EventBridge, etc.) — without writing code or managing infrastructure.

- Bi-directional — data can flow from SaaS → AWS or AWS → SaaS.
- Data can be **filtered, mapped, and transformed** during transfer (no separate ETL step needed).
- Transfers are encrypted in transit and at rest.
- **No servers to manage** — fully managed, pay per flow run.

---

## Key Concepts

- **Flow** — the configuration that defines: source, destination, trigger, field mappings, and filters.
- **Source / Destination** — the connected applications (e.g. Salesforce → S3).
- **Trigger** — when the flow runs:
  - **On demand** — manually triggered.
  - **Scheduled** — run every hour/day/week.
  - **Event-based** — triggered when something happens in the source (e.g. new Salesforce record created).
- **Field Mapping** — map source fields to destination fields, apply transformations (concatenate, mask, convert type).
- **Filter** — only transfer records matching a condition (e.g. only leads created this week).

---

## How It Works

```
SaaS App (e.g. Salesforce)
    │
    │  AppFlow pulls data (scheduled / event-triggered)
    ▼
AppFlow
    │  applies field mapping, filters, transformations
    ▼
AWS Destination (S3, Redshift, EventBridge, etc.)
```

---

## AppFlow vs Glue vs Data Pipeline

| | AppFlow | AWS Glue | Data Pipeline |
|---|---|---|---|
| **Source** | SaaS apps (Salesforce, SAP, Slack) | S3, RDS, DynamoDB, JDBC | AWS services, on-premises |
| **Code required** | No — UI-driven | Yes — PySpark / Python | Yes — pipeline scripts |
| **Transformation** | Basic (mapping, filter, mask) | Full ETL (complex transforms) | Basic |
| **Best for** | SaaS → AWS data ingestion | Large-scale ETL on AWS data | Scheduled data movement pipelines |
