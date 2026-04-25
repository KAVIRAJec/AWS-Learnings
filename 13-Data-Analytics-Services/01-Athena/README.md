## Amazon Athena

Amazon Athena is a **serverless interactive query service** that lets you analyze data stored in **S3** using standard SQL — no infrastructure to manage.

**Key Concepts:**
- Query data directly in S3 — no need to load it into a database.
- Built on **Presto/Trino** engine. Supports formats: CSV, JSON, ORC, Parquet, Avro.
- Integrates with **AWS Glue Data Catalog** for schema/table definitions.
- **Pay-per-query**: charged per TB of data scanned. Use columnar formats (Parquet, ORC) and partitioning to reduce cost.
- Results are stored in a specified S3 output location.
- Supports **federated queries** — query data across S3, RDS, DynamoDB, and on-premises sources using Lambda connectors.

**Use cases:** Ad-hoc querying on S3, log analysis (CloudTrail, ALB, VPC Flow Logs), data exploration.
