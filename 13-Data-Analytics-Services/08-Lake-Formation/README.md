## Data Lake

A **data lake** is a centralized repository that stores **all your structured, semi-structured, and unstructured data at any scale** in its raw form — you define the structure at query time ("schema-on-read"), unlike a data warehouse that requires schema upfront ("schema-on-write").

```
Raw data sources
  ├── RDS / DynamoDB          ─┐
  ├── S3 logs / clickstreams   ├──▶ S3 (data lake storage)
  ├── IoT devices / streams    │        │
  └── On-premises databases  ─┘        ├──▶ Athena      (ad-hoc SQL)
                                        ├──▶ EMR         (Spark/Hive batch)
                                        ├──▶ Redshift    (BI / reporting)
                                        └──▶ SageMaker   (ML training)
```

**Data Lake vs Data Warehouse:**

| | Data Lake | Data Warehouse |
|---|---|---|
| **Data type** | Raw — any format | Processed, structured |
| **Schema** | Schema-on-read | Schema-on-write |
| **Cost** | Low (S3) | Higher (compute + storage) |
| **Users** | Data engineers, data scientists | Analysts, BI tools |
| **Query speed** | Slower | Faster |
| **Examples** | S3 + Glue + Athena | Redshift |

**Key risk:** Without governance, a data lake becomes a **data swamp** — data with no catalog, no access control, no lineage. Lake Formation exists to prevent this.

---

## Amazon Security Lake

Amazon Security Lake is a **managed security data lake** that automatically centralizes security logs and events from AWS services, cross account, third-party sources, and on-premises into a **dedicated S3 bucket in your account** — normalized into a single format for analysis.

**Core idea:** Instead of querying CloudTrail in one place, VPC Flow Logs in another, and GuardDuty elsewhere, Security Lake pulls everything into one queryable lake.

**OCSF (Open Cybersecurity Schema Framework):**
- Security Lake normalizes all data to **OCSF** — an open standard schema.
- Every source (CloudTrail, VPC Flow Logs, Route 53 logs, GuardDuty, third-party tools) is converted into OCSF format.
- This means you write **one query** regardless of source — no format juggling.

**Log Sources:**

| Category | Sources |
|---|---|
| **AWS native** | CloudTrail (management + data events), VPC Flow Logs, Route 53 resolver logs, Security Hub findings, GuardDuty findings, Lambda execution logs, EKS audit logs |
| **Third-party** | Palo Alto, Crowdstrike, Okta, Cisco, etc. via OCSF-compatible integrations |
| **Custom** | Your own application logs — convert to OCSF and push to Security Lake |

**Architecture:**

```
AWS Sources (CloudTrail, VPC Flow Logs, GuardDuty…)
Third-party tools (Crowdstrike, Okta…)
        │
        ▼
Security Lake  (normalizes to OCSF → stores in S3 in your account)
        │
        ├──▶ Athena          (query logs with SQL)
        ├──▶ OpenSearch      (SIEM / dashboards)
        ├──▶ SageMaker       (ML-based threat detection)
        └──▶ Third-party SIEM (Splunk, IBM QRadar via subscriber)
```

**Subscribers:**
- Security Lake uses a **subscriber model** — other tools (SIEM, analytics platforms) can subscribe to receive new data as it arrives, either via S3 notification or SQS queue.

**Multi-region / multi-account:**
- Can aggregate logs from multiple AWS accounts and regions into a **single delegated Security Lake account**.
- Integrates with **AWS Organizations** — enable for all member accounts centrally.

**When to use Security Lake vs CloudTrail Lake:**

| | Security Lake | CloudTrail Lake |
|---|---|---|
| **Data scope** | Multi-source (CloudTrail + VPC + GuardDuty + third-party) | CloudTrail events only |
| **Format** | OCSF | CloudTrail event format |
| **Storage** | S3 (your account, Parquet) | Managed by AWS |
| **Use case** | Centralized SOC / SIEM feeding | CloudTrail-specific investigations |

---

## Amazon Lake Formation

Amazon Lake Formation is a managed service that simplifies **building, securing, and managing data lakes on S3** — what would take months manually can be set up in days.

**Key Concepts:**
- Built on top of **AWS Glue** — uses Glue Data Catalog as the central metadata store.
- Manages data ingestion, cataloging, cleaning, deduplication, and transformation into the data lake.
- Data stored in **S3** — Lake Formation manages permissions on top of it.

**Fine-Grained Access Control:**
- Central place to define **column-level, row-level, and cell-level** permissions on data.
- Permissions enforced across all integrated services — **Athena, Redshift Spectrum, EMR, Glue** — without managing S3 bucket policies per service.
- **Tag-based access control (TBAC)**: Assign LF-tags to databases/tables/columns and grant permissions based on tags — scalable alternative to grant-per-resource.

**Blueprints:**
- Pre-built ingestion workflows to load data from **databases (RDS, on-premises)** or **log sources** into the data lake automatically.
- Handles incremental loads — tracks which data has already been ingested.

**Cross-Account Data Sharing:**
- Share Glue Data Catalog databases and tables with other AWS accounts via **AWS RAM (Resource Access Manager)**.
- Consumers query shared data directly using Athena or Redshift Spectrum — no data copy needed.

**How it works:**
1. Register S3 location with Lake Formation.
2. Crawl and catalog data using Glue Crawlers.
3. Grant fine-grained permissions to users/roles/groups via Lake Formation console.
4. Users query via Athena/Redshift Spectrum — Lake Formation enforces permissions transparently.

**Use cases:** Centralized data governance, multi-account data sharing, securing data lakes with column/row-level access, simplifying data lake onboarding.
