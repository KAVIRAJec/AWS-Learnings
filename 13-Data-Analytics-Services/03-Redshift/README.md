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

---

## Redshift Spectrum

Redshift Spectrum lets you **query data directly in S3** using standard SQL — without loading it into Redshift first. It extends your data warehouse to your data lake.

```
Redshift Cluster (Leader Node)
    │
    ├── Compute Nodes  ──▶  queries data already loaded in Redshift
    │
    └── Spectrum Layer ──▶  scales out thousands of nodes to query S3 directly
                                    │
                            S3 (Parquet, ORC, JSON, CSV, Avro...)
```

### How It Works

1. Define an **external schema** in Redshift pointing to an **AWS Glue Data Catalog** database (or Hive Metastore).
2. Create **external tables** — metadata only (schema + S3 location), no data is moved.
3. Run a standard SQL query joining Redshift tables with external tables — Spectrum handles the S3 scan in parallel.

```sql
-- External table points to S3, no data copied into Redshift
SELECT r.customer_id, s.order_total
FROM redshift_table r
JOIN spectrum_schema.s3_orders s   -- data lives in S3
  ON r.customer_id = s.customer_id
WHERE s.order_date >= '2024-01-01';
```

### Key Characteristics

- **Scales independently** — Spectrum uses its own massive parallel fleet; your Redshift cluster size does not limit S3 query throughput.
- **Columnar formats perform best** — Parquet and ORC with predicate pushdown dramatically reduce data scanned (and cost).
- **Schema on read** — S3 data doesn't need to be pre-transformed; apply schema at query time via Glue catalog.
- **Shared catalog** — external tables defined in Glue are also visible to Athena — same metadata, different query engines.

### Pricing

You pay for Redshift Spectrum separately from your cluster — **$5 per TB of S3 data scanned**. Using compressed columnar formats (Parquet/ORC) and partition pruning minimizes scanned data and cost.

### Redshift Spectrum vs Athena

| | Redshift Spectrum | Amazon Athena |
|---|---|---|
| **Query engine** | Redshift (SQL + joins with warehouse tables) | Presto/Trino (serverless) |
| **Best for** | Joining S3 data lake with Redshift warehouse data | Ad-hoc S3 queries, no warehouse needed |
| **Requires Redshift cluster** | Yes | No |
| **Pricing** | $5/TB scanned (+ cluster cost) | $5/TB scanned only |
| **Catalog** | Glue Data Catalog | Glue Data Catalog |
