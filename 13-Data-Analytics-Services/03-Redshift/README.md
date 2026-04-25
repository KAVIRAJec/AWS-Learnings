## Amazon Redshift

Amazon Redshift is a fully managed **cloud data warehouse** for running complex analytical queries on large datasets (petabyte-scale).

**Key Concepts:**
- **Columnar storage**: Data stored by column instead of row — efficient for analytical queries that scan specific columns.
- **MPP (Massively Parallel Processing)**: Queries distributed across multiple nodes for high performance.
- **Cluster**: Made up of a **leader node** (query planning) and **compute nodes** (execution).
- **Redshift Serverless**: Run analytics without managing clusters — auto-scales capacity.
- **Redshift Spectrum**: Query data directly in S3 without loading it into Redshift — extends warehouse to a data lake.
- **Enhanced VPC Routing**: Forces all COPY/UNLOAD traffic through your VPC.
- Supports **snapshots** (automatic & manual) stored in S3 for backup and cross-region copy.
- Integrates with S3, Glue, Athena, QuickSight, and BI tools (Tableau, Looker).

**Use cases:** Data warehousing, business intelligence, large-scale analytics, reporting.
