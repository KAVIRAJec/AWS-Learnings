## Amazon Lake Formation

Amazon Lake Formation simplifies **building, securing, and managing data lakes** on top of S3.

**Key Concepts:**
- Automates data ingestion, cataloging, cleaning, and transformation into a data lake in S3.
- Built on top of **AWS Glue** — uses Glue Data Catalog for metadata management.
- **Fine-grained access control**: Column-level and row-level permissions on data — controls who can see what in Athena, Redshift Spectrum, or EMR.
- **Blueprints**: Pre-built workflows to ingest data from databases or logs into the data lake.
- **Cross-account sharing**: Share data catalog resources securely with other AWS accounts.

**Use cases:** Centralizing data from multiple sources, enforcing data governance, simplifying data lake setup.
