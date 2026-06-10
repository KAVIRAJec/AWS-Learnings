## Amazon Aurora

Amazon Aurora is a MySQL and PostgreSQL-compatible relational database built for the cloud — 5x faster than MySQL, 3x faster than PostgreSQL. ~20% costlier than standard RDS.

**Key Concepts:**
- **Storage**: Auto-increments in 10 GB steps up to 128 TB — shared across all instances in the cluster.
- **Availability**: 6 copies of data across 3 AZs. 4/6 needed for writes, 3/6 for reads. Self-healing storage, auto-expanding.
- **Failover**: Automatic — no manual intervention needed. Behavior depends on your cluster configuration:

  | Scenario | Failover Behavior |
  |---|---|
  | **Aurora Replica exists** | Aurora flips the **CNAME** of the DB instance to point at the healthy replica → replica is promoted to primary. Completes in **< 30 seconds**. |
  | **Single instance (no replica), not Serverless** | Aurora attempts to create a new DB instance in the **same AZ** as the original — best-effort, may not succeed if the AZ itself has issues. |
  | **Aurora Serverless** | Aurora automatically recreates the DB instance in a **different AZ**. |

  > Aurora flips the **CNAME** record (not an A record / IP address) to redirect traffic to the new primary — applications reconnect via the same endpoint without any DNS change on their side.
- **Read Replicas**: Up to 15 read replicas with auto-scaling support. Replication is **asynchronous with milliseconds of lag** (< 1 second) — much lower than standard RDS MySQL replicas.

**Aurora Replicas vs MySQL (RDS) Replicas:**

| Feature | Aurora Replicas | MySQL (RDS) Replicas |
|---|---|---|
| **Number of replicas** | Up to 15 | Up to 5 |
| **Replication type** | Asynchronous (**milliseconds**) | Asynchronous (**seconds**) |
| **Performance impact on primary** | Low | High |
| **Replica location** | In-region | Cross-region supported |
| **Failover target** | Yes — **no data loss** | Yes — potentially **minutes of data loss** |
| **Automated failover** | Yes | No |
| **User-defined replication delay** | No | Yes |
| **Different schema vs primary** | No | Yes |

**Cluster Endpoints:**
- **Writer Endpoint**: Points to the primary instance — handles reads and writes (DDL + DML). On failover, automatically re-points to the new primary.
- **Reader Endpoint**: Load-balances read traffic across **all** read replicas — no control over which replica handles a connection.
- **Custom Endpoint**: Route traffic to a **specific subset** of instances you define — by instance class, parameter group, or any criteria. Aurora load-balances within that subset.
  - Use case: Direct production traffic to **high-capacity instances** via one custom endpoint, and reporting/analytics queries from internal staff to **low-capacity instances** via another custom endpoint.
  - The reader endpoint does not support this — it always distributes across all replicas with no capacity-based routing.
  - Aurora does **not** do capacity-based routing automatically — you must create custom endpoints explicitly.

| Endpoint | Points to | Use when |
|---|---|---|
| Writer | Primary instance only | Writes, DDL statements |
| Reader | All replicas (load-balanced) | Read-only queries, no capacity preference |
| Custom | Subset of instances you define | Route by capacity class, workload type, or user group |

**Backups:**
- **Automatic**: Continuous and incremental — PITR within 1–35 days. Default retention is **1 day**. **Cannot be disabled** (unlike RDS). No performance impact or interruption while backup data is being written. Stored in S3.
- **Manual Snapshot**: Take a snapshot to retain data **beyond the retention period** — stored until explicitly deleted. Use to create a new DB cluster from a point-in-time copy.
- **Restore options**: From snapshot or using **Percona XtraBackup** (from S3).

**Aurora Serverless:**
- On-demand, auto-scaling — automatically starts, shuts down, and scales based on load.
- Accessed via a proxy endpoint managed by AWS.
- Use case: Infrequent or unpredictable workloads (flash sales, dev/test, variable traffic).

**Migrating from Provisioned Aurora to Aurora Serverless:**
- **Cannot convert in-place** — changing the instance class from Provisioned to Serverless is not supported. You cannot simply modify the cluster.
- **Options and their trade-offs:**

| Method | Downtime | Notes |
|---|---|---|
| **AWS DMS (Full Load + CDC)** | Near-zero | Source stays live; CDC keeps target in sync until cutover — recommended |
| Snapshot restore | Yes — app must stop until new cluster is ready | Simple but causes full downtime |
| Aurora Read Replica promotion | Brief write unavailability during failover | Valid but app cannot write during the failover window |

> For minimal downtime, use **DMS with Full Load + CDC** — the source provisioned cluster stays fully operational while DMS continuously replicates changes to the new Aurora Serverless cluster. Cut over by updating the application's connection string when the target is in sync.

**Aurora Backtrack:**
- **Rewinds** an Aurora DB cluster to a specific point in time — without restoring from a backup or creating a new cluster.
- Works by undoing changes in-place — the same cluster, same endpoint, same data, just rolled back to an earlier state.
- **Aurora MySQL only** — not supported on Aurora PostgreSQL.
- Backtrack window configurable up to 72 hours.
- **Not a replacement for backups** — use backups/snapshots for disaster recovery. Backtrack is for quickly undoing accidental operations (e.g., DELETE without WHERE clause, dropped table).
- **Cannot recover from AZ or region failures** — backtrack is a data correction tool, not an HA/DR mechanism.

**Aurora Global Database:**
- Single Aurora DB spanning multiple regions — primary (1) + secondary read-only regions (up to 10).
- Up to 16 read replicas per secondary region.
- Replication lag < 1 second. RTO < 1 minute.

**Aurora Database Cloning:**
- Creates a copy of the cluster faster than snapshot/restore.
- Uses **copy-on-write** — new cluster shares original data until changes are made, then allocates separate storage.

**Aurora Native Lambda Invocation:**
- Aurora MySQL can invoke a Lambda function **directly from within the database** using built-in native functions — triggered by SQL events like row inserts, updates, or deletes.
- Two functions:
  - `lambda_sync(function_arn, payload)` — **synchronous** call, waits for Lambda response before continuing.
  - `lambda_async(function_arn, payload)` — **asynchronous** call, fires and forgets — does not wait for Lambda.
- Use case: Capture data changes (INSERT, UPDATE, DELETE) at the database level and push them to downstream systems (SQS, SNS, another service) — without polling or CDC pipelines.

```sql
-- Example: invoke Lambda when a vehicle listing is deleted
CALL mysql.lambda_async(
  'arn:aws:lambda:us-east-1:123:function:ProcessDeletedListing',
  JSON_OBJECT('listing_id', OLD.id, 'model', OLD.model)
);
```

> **RDS Event Subscriptions** are for **infrastructure-level events** (instance failover, backup completed, storage full) — they do NOT capture data changes like INSERT/UPDATE/DELETE. Use Aurora native functions for data-level triggers.

**Aurora Machine Learning:**
- Run ML models within SQL queries. Integrates with **SageMaker** (general ML) and **Comprehend** (sentiment analysis).

**BabelFish:**
- Allows Microsoft SQL Server applications to run on Aurora PostgreSQL without code changes — translates T-SQL to PostgreSQL.

**Encryption:**
- Same as RDS — KMS at rest (must be set at launch), SSL/TLS in transit.

---

## Aurora Parallel Query

Aurora Parallel Query pushes down the computation of analytical queries to the **Aurora storage layer** — the query is processed across thousands of storage nodes in parallel instead of pulling all data up to the DB instance first.

**How it works without Parallel Query:**
```
DB Instance
    │  pulls raw data from storage
    ▼
All filtering, aggregation, and processing happen on the DB instance CPU/RAM
    → bottleneck on the instance for large scans
```

**How it works with Parallel Query:**
```
DB Instance
    │  sends query plan to storage layer
    ▼
Aurora Storage Nodes (thousands)
    │  each node scans its local data in parallel
    │  filtering and aggregation happen in storage
    ▼
DB Instance receives pre-processed, reduced result set
    → much less data transferred, much less CPU used on instance
```

**Key properties:**
- Speeds up analytical queries that scan **large amounts of data** (full table scans, aggregations, joins) — typically 10–100x faster.
- Works on the **same data as transactional queries** — no ETL, no separate data copy, no replication lag.
- Does **not** require a separate read replica — parallel query runs on the primary or existing replicas.
- Available for **Aurora MySQL** only (not Aurora PostgreSQL).
- Must be enabled at the DB cluster parameter group level.

**When to use:**
- OLAP-style queries on large Aurora MySQL tables alongside transactional (OLTP) workloads.
- Avoid spinning up a separate data warehouse (Redshift) for occasional large analytical queries.
- Use cases: reporting, aggregations, log analysis, business intelligence queries directly on Aurora.

**Parallel Query vs Aurora Serverless vs Read Replicas:**

| | Parallel Query | Read Replica | Separate Redshift |
|---|---|---|---|
| **Purpose** | Speed up large analytical queries | Scale read throughput | Full data warehouse |
| **Data freshness** | Real-time (same data as primary) | Near real-time (replica lag) | ETL delay |
| **Setup** | Enable in parameter group | Launch replica instance | Full ETL pipeline |
| **Best for** | Occasional large scans on existing Aurora | High read concurrency | Complex ongoing analytics |

---

## Aurora Storage Classes

Aurora offers two storage configurations — choose based on your I/O pattern:

| | Aurora Standard | Aurora I/O-Optimized |
|---|---|---|
| **Best for** | Workloads where I/O cost < 25% of total Aurora bill | I/O-intensive workloads where I/O cost > 25% of total bill |
| **I/O cost** | Charged per I/O operation | **Included** — no per-I/O charge |
| **Storage cost** | Lower | Higher per GB |
| **Pricing model** | Pay for compute + storage + I/O separately | Pay for compute + storage (I/O bundled) |
| **Switch** | Can switch to I/O-Optimized at any time | Can switch back to Standard after 30 days |

**When to choose I/O-Optimized:**
- High read/write throughput workloads — analytics, financial transactions, real-time applications.
- Your I/O costs are unpredictable or consistently high.
- You want **predictable pricing** — no surprise I/O bills.

**When to stay on Standard:**
- Dev/test environments.
- Workloads with low or intermittent I/O.
- I/O costs are a small fraction of your total Aurora spend.

---

## Migrating to Aurora

### MySQL Migrations

**From RDS MySQL → Aurora MySQL:**

Option 1 — DB Snapshot restore:
```
RDS MySQL ──► take snapshot ──► restore as Aurora MySQL cluster
```
- Simplest method — no extra infrastructure needed.
- Brief downtime during final cutover.

Option 2 — Aurora Read Replica promotion:
```
RDS MySQL (primary, stays live)
    │  replication
    ▼
Aurora MySQL Read Replica
    │  wait until replication lag = 0
    ▼
Promote → standalone Aurora MySQL cluster
```
- Near-zero downtime — promote only when lag hits 0.
- Takes time and costs $ (replica running alongside primary).

**From External MySQL → Aurora MySQL:**

Option 1 — Percona XtraBackup via S3 (faster):
```
External MySQL ──► Percona XtraBackup ──► S3 ──► import ──► Aurora MySQL
```
- Physical backup — faster than SQL dump for large databases.

Option 2 — mysqldump (simpler, slower):
```
External MySQL ──► mysqldump ──► Aurora MySQL
```
- Slower than XtraBackup — generates SQL statements instead of raw files.
- Good for small databases.

> Use **DMS** if both source and target databases are up and running (near-zero downtime with CDC).

---

### PostgreSQL Migrations

**From RDS PostgreSQL → Aurora PostgreSQL:**

Option 1 — DB Snapshot restore:
```
RDS PostgreSQL ──► take snapshot ──► restore as Aurora PostgreSQL cluster
```

Option 2 — Aurora Read Replica promotion:
```
RDS PostgreSQL (primary, stays live)
    │  replication
    ▼
Aurora PostgreSQL Read Replica
    │  wait until replication lag = 0
    ▼
Promote → standalone Aurora PostgreSQL cluster
```
- Near-zero downtime — same pattern as MySQL.

**From External PostgreSQL → Aurora PostgreSQL:**

```
External PostgreSQL ──► pg_dump backup ──► S3 ──► import via aws_s3 Aurora extension ──► Aurora PostgreSQL
```
- Uses the built-in `aws_s3` Aurora extension to load the backup directly from S3 into Aurora.

> Use **DMS** if both source and target databases are up and running.

---

### Migration Method Summary

| Scenario | Method | Downtime |
|---|---|---|
| RDS MySQL → Aurora MySQL | Snapshot restore | Brief |
| RDS MySQL → Aurora MySQL | Aurora Read Replica + promote (lag = 0) | Near-zero |
| External MySQL → Aurora MySQL | Percona XtraBackup → S3 → import | Brief |
| External MySQL → Aurora MySQL | mysqldump (slower) | Brief |
| RDS PostgreSQL → Aurora PostgreSQL | Snapshot restore | Brief |
| RDS PostgreSQL → Aurora PostgreSQL | Aurora Read Replica + promote (lag = 0) | Near-zero |
| External PostgreSQL → Aurora PostgreSQL | pg_dump → S3 → aws_s3 extension import | Brief |
| Any (both DBs running) | AWS DMS (Full Load + CDC) | Near-zero |
