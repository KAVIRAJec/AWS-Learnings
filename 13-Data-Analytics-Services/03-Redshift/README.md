## Amazon Redshift

Amazon Redshift is a fully managed **petabyte-scale cloud data warehouse** — designed for OLAP (Online Analytical Processing) and complex analytical queries. It is a postgres based data warehouse, not an OLTP system. It stores **Structured and Semi-Structured** data (JSON, Parquet, ORC) and is optimized for read-heavy workloads.

**Key Concepts:**
- **Columnar Storage**: Data stored by column — efficient for analytics that scan specific columns across millions of rows.
- **MPP (Massively Parallel Processing)**: Queries split and executed in parallel across multiple compute nodes.
- **Cluster**: Consists of one **Leader Node** (query planning, result aggregation) and one or more **Compute Nodes** (actual execution). Single-node clusters also supported for small workloads.
- **Node Types**:
   - **RA3**: Separates compute and storage — storage in S3-backed managed storage, scales independently.
   - **DC2**: Dense compute — fast SSD-based local storage, fixed storage per node.
- **Redshift Serverless**: Automatically provisions and scales capacity — no cluster management. Pay per RPU (Redshift Processing Unit) per second.
- **Redshift Spectrum**: Query data directly in **S3** without loading into Redshift — extends your data warehouse to a data lake. Uses external tables defined in Glue Data Catalog.

**Loading Data:**
- **COPY command**: Most efficient way to load data — parallel load from S3, DynamoDB, EMR, or SSH.
- **INSERT**: Supported but slower — not recommended for large loads.
- Data should be loaded in **sorted order** by sort key for best performance.

**Performance:**
- **Sort Keys**: Define the order data is stored on disk — speeds up range queries and filters.
- **Distribution Keys**: Controls how data is distributed across nodes — reduces data movement during joins.
- **Materialized Views**: Pre-compute and cache complex query results — auto-refreshed when base data changes.
- **Result Caching**: Identical queries return cached results instantly — no compute cost.

**Backup & Recovery:**
- **Point-in-time recovery**: Rretained for 1–35 days. Stored in S3 internally.
- **Automatic snapshots**: Every 8 hours or every 5 GB of data change or scheduled. Retained 1–35 days.
- **Manual snapshots**: Retained until deleted. Can be copied to other regions and create new clusters from snapshots.
- Can configure automatic snapshot copy to another region for disaster recovery.

**Security:**
- **Enhanced VPC Routing**: Forces all COPY/UNLOAD traffic through your VPC instead of the internet.
- Encryption at rest (KMS) and in transit (SSL).
- Supports IAM, VPC, and column-level/row-level security.

**Integrations:** S3, Glue, Athena, QuickSight, SageMaker, BI tools (Tableau, Looker, Power BI).

**Use cases:** Data warehousing, business intelligence dashboards, large-scale reporting, combining data lake (S3) with structured warehouse queries.
