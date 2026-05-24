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

---

## Glue Studio vs Glue DataBrew

Both are visual, no-/low-code interfaces inside Glue — but they serve very different personas and purposes.

| | Glue Studio | Glue DataBrew |
|---|---|---|
| **Who it's for** | Data engineers | Data analysts / business users |
| **What it builds** | Full ETL pipelines (Spark jobs) | Data preparation recipes |
| **Code involved** | Visual → generates PySpark/Scala underneath(for advanced users can edit code - need to know the underlying code) | No code at all — 250+ point-and-click transformations |
| **Data profiling** | No | Yes — shows column stats, missing values, distributions |
| **Output** | ETL job ready to run at scale on any data size | Cleaned dataset written to S3 or back to source |
| **Sources** | S3, Redshift, RDS, JDBC, Kinesis, Kafka | S3, Redshift, RDS, Glue Data Catalog |
| **Scheduling** | Via Glue Workflows / triggers | Built-in job scheduling |
| **Scale** | Spark cluster (DPUs) — built for large data | Single-node managed environment — suited for samples and mid-size data |
| **Underlying engine** | Apache Spark | Apache Spark (but abstracted fully) |

**One-line memory trick:**
- **Glue Studio** = *engineer builds a pipeline* (drag-and-drop Spark ETL)
- **Glue DataBrew** = *analyst cleans a dataset* (Excel-like transformations, profiling, no code)
