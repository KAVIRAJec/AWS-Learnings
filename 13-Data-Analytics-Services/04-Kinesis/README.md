## Amazon Kinesis

Amazon Kinesis is a platform for **real-time data streaming and analytics**. It has four services:

### Kinesis Data Streams (KDS):
- Ingest and process real-time streaming data (logs, events, clickstreams).
- Data split into **shards** — each shard handles 1 MB/s ingestion, 2 MB/s read. Can send upto 10 MB of data to KDS.
- Data retained for **1 day (default)**, up to **365 days**. Data send to Kinesis can't be modified or deleted until retention expires.
- Consumers (EC2, Lambda, Kinesis Data Analytics) process data in real time.
- **Provisioned mode**: You manage shard count. Pay each shard provisioned per hour. **On-demand mode**: Auto-scales. Default capacity provisioned is 200 MB/s. Pay per stream per hourr and data in/out per GB.
- **Encryption**: In-flight via HTTPS, at-rest via SSE with KMS, client-side encryption supported by SDKs.
- **Kinesis Producer Library (KPL)**: Java library to simplify & optimize producing to KDS, handles batching, retries, and load balancing.
- **Kinesis Client Library (KCL)**: Java library to simplify & optimize consuming from KDS, handles load balancing, checkpointing, and retries.
- **Producer**:
    - **Kinesis Agent**: Standalone Java application to monitor files and send new data to KDS (e.g., log files).
    - **Kinesis Producer Library (KPL)**: Java library to simplify and optimize producing to KDS, handles batching, retries, and load balancing.
    - **AWS SDK**: Use SDKs to send data to KDS from applications.
- **Consumer**:
    - **Kinesis Data Analytics**: Run SQL or Apache Flink queries on streaming data from KDS in real time.
    - **Kinesis Data Firehose**: Load streaming data from KDS into destinations (S3, Redshift, OpenSearch, Splunk, HTTP endpoints).
    - **Kinesis Client Library (KCL)**: Java library to simplify and optimize consuming from KDS, handles load balancing, checkpointing, and retries.

### Kinesis Data Firehose:
- Loads streaming data into destinations — **S3, Redshift, OpenSearch, Splunk, HTTP endpoints**.
- Fully managed & serverless, no shards to manage. Near real-time — Firehose **buffers** incoming data before delivering to the destination. It waits until either the buffer size (e.g., 1 MB) or buffer interval (e.g., 60s) is reached, whichever comes first, then flushes the batch. This is why it's "near real-time" and not instant.
- Output results to S3, Redshift, OpenSearch, or another Kinesis stream. 3rd party partner destination and Custom destination(using HTTP endpoint)
- Can transform data using **Lambda** before delivery. Failed data can be sent to S3 Backup bucket for debugging.
- No data storage — delivers and done.
- Supports CSV, JSON, Parquet, Avro, ORC, Raw text and binaries. Can convert JSON to Parquet/ORC formats for efficient storage and querying in Redshift or OpenSearch.

### Kinesis Data Analytics:
- Run **SQL or Apache Flink** queries on streaming data from KDS or Firehose in real time.
- Record can be upto 1MB.
- Output results to S3, Redshift, OpenSearch, or another Kinesis stream.
- Use case: Real-time dashboards, anomaly detection, metric aggregation.

### Kinesis Video Streams:
- Ingest, process, and store video streams from connected devices (cameras, IoT).
- Use case: Video analytics, ML on video data, security surveillance.

**KDS vs Firehose:**
- KDS: Real-time, custom consumers, data retention, replay possible.
- Firehose: Near real-time, managed delivery to destinations, no retention.
