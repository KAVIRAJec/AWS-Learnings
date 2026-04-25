## Amazon Glue

Amazon Glue is a fully managed, **serverless ETL (Extract, Transform, Load)** service for preparing and transforming data for analytics.

**Key Concepts:**
- **Glue Data Catalog**: Central metadata repository — stores table definitions, schemas, and connection info. Used by Athena, Redshift Spectrum, and EMR.
- **Glue Crawler**: Automatically scans data sources (S3, RDS, DynamoDB) and infers schemas, populating the Data Catalog.
- **Glue Jobs**: ETL scripts (Python/Spark) that transform and move data between sources and destinations.
- **Glue Studio**: Visual interface to create, run, and monitor ETL jobs without writing code.
- **Glue DataBrew**: Visual data preparation tool — clean and normalize data without coding.
- Serverless — no cluster management. Scales automatically.

**Use cases:** Data preparation for analytics, building data lakes, schema discovery, transforming raw S3 data into queryable formats.
