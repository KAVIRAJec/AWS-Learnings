## Amazon Athena

Amazon Athena is a **serverless interactive query service** that lets you analyze data stored in **S3** using standard SQL — no infrastructure to manage, no data loading required.

**Key Concepts:**
- Built on **Presto/Trino** engine. Supports formats: CSV, JSON, ORC, Parquet, Avro, and compressed files (gzip, snappy).
- Integrates with **AWS Glue Data Catalog** for schema and table definitions — Glue crawlers can auto-discover schemas. Use Glue to convert you data to ORC or Parquet.
- Results stored in a specified **S3 output location**.
- **Pay-per-query**: $5 per TB of data scanned. Cancelled queries are still charged for data scanned so far.
- **Schema-on-read**: Define schema at query time, not when data is stored. Flexible for evolving data formats. It does not store your data; instead, it reads it directly from Amazon S3. The "table" acts as a metadata layer that tells Athena how to interpret the raw files sitting in your bucket.

**Performance & Cost Optimization:**
- Use **columnar formats** (Parquet, ORC) — reduces data scanned significantly (up to 90% savings).
- Use **partitioning** — filter by partition keys in S3 (e.g., `year/month/day`) to scan only relevant data.
- Use **compression** — reduces file size and scan cost.
- Combine small files into larger ones to avoid scanning many small objects.

**Federated Queries:**
- Query data outside S3 — RDS, DynamoDB, Redshift, on-premises DB sources — using **data source connectors that run on Lambda**.
- Results can be stored back in S3.

**Athena for Apache Spark:**
- Run interactive **Apache Spark** workloads on Athena — serverless, no cluster management.
- Use for ML preprocessing, complex transformations, and notebook-based analytics.

**Use cases:** Ad-hoc SQL on S3, log analysis (CloudTrail, ALB, VPC Flow Logs, S3 Access Logs), data lake querying, cost-effective BI on raw data.
