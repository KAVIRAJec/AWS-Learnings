## Amazon Glue

Amazon Glue is a fully managed, **serverless ETL (Extract, Transform, Load)** service for discovering, preparing, and transforming data for analytics — no infrastructure to manage.

**Key Components:**

- **Glue Data Catalog**:
   - Central **metadata repository** — stores table definitions, schemas, partitions, and connection info.
   - Acts as a Hive-compatible metastore — used by **Athena, Redshift Spectrum, and EMR** automatically.
   - One Data Catalog per AWS region per account.

- **Glue Crawler**:
   - Scans data sources (S3, RDS, DynamoDB, JDBC) and automatically **infers schemas** — populates the Data Catalog.
   - Can be scheduled or triggered on demand.

- **Glue Jobs**:
   - ETL scripts written in **Python or Scala (Spark)** that transform and move data between sources and destinations.
   - Runs on a managed Spark environment — you set the number of DPUs (Data Processing Units).
   - **Job Bookmarks**: Track which data has already been processed — avoids reprocessing on incremental loads.

- **Glue Studio**: Visual drag-and-drop interface to build, run, and monitor ETL jobs without writing code.

- **Glue DataBrew**: No-code visual data preparation tool — profile, clean, and normalize data using 250+ built-in transformations. Targeted at data analysts.

- **Glue Workflows**: Orchestrate multiple crawlers, jobs, and triggers into a single automated pipeline.

**Glue Streaming ETL:**
- Process streaming data from **Kinesis Data Streams, Kafka, or MSK** in near real-time using Glue jobs with Spark Structured Streaming.

**Common Pattern:**
S3 (raw) → Glue Crawler (schema) → Glue Job (transform to Parquet) → S3 (processed) → Athena / Redshift Spectrum (query)

**Use cases:** Data lake ingestion, schema discovery, raw-to-curated data transformation, converting CSV/JSON to Parquet/ORC, incremental data processing.
