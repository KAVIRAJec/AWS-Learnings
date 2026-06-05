## Amazon Kinesis

Amazon Kinesis is AWS's platform for **real-time data streaming**. Four purpose-built services handle ingestion, delivery, analytics, and video.

| Service | Purpose |
|---|---|
| **Kinesis Data Streams (KDS)** | Real-time ingestion + custom processing with replay |
| **Kinesis Data Firehose** | Managed delivery to destinations (S3, Redshift, OpenSearch…) |
| **Kinesis Data Analytics** | Real-time SQL / Apache Flink queries on streams |
| **Kinesis Video Streams** | Ingest, store, and analyze video from devices |

---

## 1. Kinesis Data Streams (KDS)

### How It Works

```
Producers                     Stream (Shards)             Consumers
─────────                     ───────────────             ─────────
SDK / KPL       ──record──▶   Shard 1  (1 MB/s in)  ──▶  Lambda
Kinesis Agent               + Shard 2  (2 MB/s out)  ──▶  KCL App
IoT / App                   + Shard N                ──▶  Firehose
```

- A **stream** is made up of **shards** — the unit of capacity.
- Each record has: **Partition Key** + **Sequence Number** + **Data Blob** (up to 1 MB).
- The **partition key** determines which shard the record lands in (consistent hashing) — same partition key always goes to the same shard, guaranteeing **ordering within a shard**.
- Data is **immutable** — once written, it cannot be modified or deleted until the retention window expires.

### Shard Capacity

| Direction | Limit per Shard |
|---|---|
| Ingestion (write) | 1 MB/s **or** 1,000 records/s |
| Consumption (read — shared) | 2 MB/s across **all** consumers on that shard |
| Consumption (Enhanced Fan-Out) | 2 MB/s **per consumer** per shard — dedicated, push model |

> **Enhanced Fan-Out** uses `SubscribeToShard` (HTTP/2 push) instead of `GetRecords` (polling). Use it when multiple consumers need full throughput independently.

**`ProvisionedThroughputExceededException`** — thrown when a shard's write limit (1 MB/s or 1,000 records/s) is exceeded.

| Fix | Effect | Cost |
|---|---|---|
| **Use `PutRecords` (batch)** | Send multiple records in one API call — reduces per-call overhead, maximizes shard throughput | No extra cost — best option |
| Increase shard count | More shards = more capacity | Higher cost per shard-hour |
| Exponential Backoff | Retries with delay — doesn't fix root cause, fails again under sustained load | No extra cost but doesn't solve the problem |
| Decrease retention duration | Has no effect on write throughput | Risk of data loss |

> **Root cause**: Calling `PutRecord` (single record) in a loop at high rate burns through the 1,000 records/s limit per shard quickly. Switch to `PutRecords` to batch up to **500 records per API call** — same shard limit but far fewer API calls, higher effective throughput.

### Data Retention & Replay

- Default retention: **24 hours**. Configurable up to **365 days**.
- **Replay** — consumers can re-read records from any point within the retention window. This is KDS's key advantage over Firehose.

### Capacity Modes

| Mode | Management | Pricing |
|---|---|---|
| **Provisioned** | You set shard count manually | Per shard-hour + data in/out per GB |
| **On-Demand** | Auto-scales (default 200 MB/s) | Per stream-hour + data in/out per GB |

### Hot Shard Problem

If your partition key has low cardinality (e.g., `country` with only a few values), most records land on the same shard → that shard gets overloaded while others are idle.

**Fix**: Use a highly cardinal or randomized partition key (e.g., `userId`, `deviceId`, or a random UUID suffix).

### Shard Splitting & Merging

- **Split** one shard into two → doubles write capacity (costs more).
- **Merge** two adjacent shards into one → halves capacity (saves cost).
- Both operations are done via the console or API; hot shards trigger splitting.

### Producers

| Producer | When to Use |
|---|---|
| **AWS SDK** (`PutRecord` / `PutRecords`) | Simple, direct writes from any app |
| **Kinesis Producer Library (KPL)** | High-throughput Java apps — handles batching, compression, retries, automatic aggregation |
| **Kinesis Agent** | Tail log files on EC2/on-prem servers and stream lines to KDS automatically |

### Consumers

**Multiple consumers can read the same KDS stream simultaneously and independently** — each consumer maintains its own position (sequence number / shard iterator) in the stream. One consumer reading does not affect another. This is the key difference from SQS, where a message is consumed by only one consumer.

```
KDS Stream (Shard 1)
        │
        ├──▶ Lambda         (reads independently, position A)
        ├──▶ KCL App        (reads independently, position B)
        ├──▶ Firehose       (reads independently, position C)
        └──▶ Custom App     (reads independently, position D)
```

**Two consumption models:**

| Model | Throughput | How |
|---|---|---|
| **Standard (shared)** | 2 MB/s shared across ALL consumers on a shard | `GetRecords` polling — consumers compete for the same 2 MB/s |
| **Enhanced Fan-Out (dedicated)** | 2 MB/s **per consumer** per shard | `SubscribeToShard` HTTP/2 push — each consumer gets its own pipe |

Use **Enhanced Fan-Out** when you have 2+ consumers and each needs full throughput — without it, consumers share and throttle each other.

| Consumer | Model | Use Case |
|---|---|---|
| **AWS SDK** (`GetRecords`) | Pull (polling, shared) | Simple custom consumers |
| **Kinesis Client Library (KCL)** | Pull — handles checkpointing, load balancing across workers | Distributed consumer apps (Java) |
| **Enhanced Fan-Out** (`SubscribeToShard`) | Push (HTTP/2, dedicated) | Multiple consumers each needing full 2 MB/s |
| **Lambda** | Event-source mapping | Serverless processing per batch of records |
| **Kinesis Data Firehose** | Managed | Load KDS records into S3, Redshift, etc. |
| **Kinesis Data Analytics** | Managed | SQL / Flink queries on the stream |

---

## 2. Kinesis Data Firehose

Fully managed, **serverless** delivery pipeline — no shards, no consumers to manage. Receives records, optionally transforms them, buffers, then flushes to a destination.

### How It Works

```
Sources              Buffer (size OR interval)     Transform     Destination
───────              ────────────────────────      ─────────     ───────────
Direct PUT  ──▶      [ 1 MB – 128 MB          ]              ──▶  S3
KDS         ──▶   OR [ 60 s – 900 s           ] ──▶ Lambda   ──▶  Redshift
MSK         ──▶      (whichever comes first)                  ──▶  OpenSearch
CloudWatch                                                    ──▶  Splunk / HTTP
```

This buffering is why Firehose is **near real-time**, not instant — the minimum latency is ~60 seconds.

### Sources

Direct PUT (SDK), Kinesis Data Streams, Amazon MSK, CloudWatch Logs, CloudWatch Events, AWS WAF, AWS IoT.

### Destinations

| Category | Destinations |
|---|---|
| **AWS** | S3, Redshift (via S3 intermediate), OpenSearch Service |
| **3rd Party** | Splunk, Datadog, New Relic, MongoDB, Coralogix |
| **Custom** | Any HTTP endpoint |

### Data Transformation

- Invoke a **Lambda function** inline — convert format, enrich with lookups, filter records.
- Lambda invocation is **synchronous** and constrained to a **5-minute (300s) timeout** — if the Lambda takes longer, the record fails.
- Combined with Firehose's buffering (minimum ~60s), complex processing logic is a brittle fit for Firehose.
- **Format conversion** (no Lambda needed): JSON → Parquet or ORC using a Glue Data Catalog schema — output is columnar and query-optimized in S3.

> **Firehose Lambda vs KDS Lambda consumer**: If you need flexible, long-running, or stateful processing logic, use a **Lambda function as a KDS consumer** instead — it has a 15-minute timeout, full access to the stream, and can fan out results anywhere. Firehose Lambda transformation is only suited for lightweight, fast record-level transforms.

### Failed Records

- Records that fail transformation or delivery can be written to an **S3 backup bucket** for debugging and reprocessing.

### Key Facts

- **No data storage** — Firehose delivers and discards. No replay.
- **No shard management** — fully auto-scaled.
- Supports: CSV, JSON, Parquet, ORC, Avro, raw text, binary.

---

## 3. Kinesis Data Analytics (Managed Apache Flink)

Run **real-time queries** on streaming data using SQL or Apache Flink (Java / Scala / Python). AWS provisions, scales, and manages the compute automatically.

### Sources

**Kinesis Data Streams** and **Amazon MSK** — Firehose is NOT a supported source.

### Processing

- **SQL mode**: simple filtering, aggregation, anomaly detection using standard SQL.
- **Apache Flink mode**: full stateful stream processing — windowing, joins, complex transformations.

**Windowing** (Flink):

| Window Type | Description | Example |
|---|---|---|
| **Tumbling** | Fixed non-overlapping time slots | Count events every 1 minute |
| **Sliding** | Fixed size, overlapping by step | Last 5 min, updated every 1 min |
| **Session** | Grouped by activity gap | User session ends after 30s idle |

### Outputs

S3, Redshift, OpenSearch, Kinesis Data Streams, Kinesis Data Firehose.

### Use Cases

Real-time dashboards, anomaly detection, metric aggregation, fraud detection, live leaderboards.

---

## 4. Kinesis Video Streams

Ingest, durably store, and process **video streams** from connected devices (IP cameras, drones, smartphones, IoT sensors).

- Supports live and on-demand playback via HLS or DASH.
- Integrates with **Amazon Rekognition Video** for real-time face detection and object recognition.
- Integrates with **SageMaker** for custom ML inference on video frames.
- Encrypted in transit (TLS) and at rest (KMS).
- Data retained for configurable duration (hours to years).

**Use cases:** Security surveillance, smart home devices, industrial monitoring, ML on video.

---

## Kinesis Services Comparison

| | KDS | Firehose | Data Analytics | Video Streams |
|---|---|---|---|---|
| **Real-time** | Yes (ms) | Near real-time (buffer) | Yes (ms–s) | Yes |
| **Data retention / Replay** | Yes (1–365 days) | No | No | Yes |
| **Managed** | Partially (shards) | Fully serverless | Fully managed | Fully managed |
| **Custom consumers** | Yes | No | No | Yes |
| **Transformation** | In consumer code | Lambda / format convert | SQL / Flink | ML models |
| **Destinations** | Any (custom code) | S3, Redshift, OpenSearch… | S3, KDS, Firehose… | Rekognition, SageMaker |
| **Max record size** | 1 MB | 1 MB | 1 MB | Video frames |

---

## KDS vs SQS vs SNS (Quick Reference)

| | KDS | SQS | SNS |
|---|---|---|---|
| **Model** | Ordered stream per shard | Queue (pull) | Pub/Sub (push) |
| **Ordering** | Per shard (partition key) | FIFO queue only | No ordering |
| **Replay** | Yes (within retention) | No | No |
| **Multiple consumers** | Yes (fan-out) | No (one consumer per message) | Yes (multiple subscribers) |
| **Use case** | Real-time analytics, event replay | Task queues, decoupling | Notifications, fan-out |
