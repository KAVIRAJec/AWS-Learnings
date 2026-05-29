## Amazon Aurora

Amazon Aurora is a MySQL and PostgreSQL-compatible relational database built for the cloud — 5x faster than MySQL, 3x faster than PostgreSQL. ~20% costlier than standard RDS.

**Key Concepts:**
- **Storage**: Auto-increments in 10 GB steps up to 128 TB — shared across all instances in the cluster.
- **Availability**: 6 copies of data across 3 AZs. 4/6 needed for writes, 3/6 for reads. Self-healing storage, auto-expanding.
- **Failover**: Automatic failover to a read replica in < 30 seconds.
- **Read Replicas**: Up to 15 read replicas with auto-scaling support.

**Cluster Endpoints:**
- **Writer Endpoint**: Points to the primary instance — handles reads and writes.
- **Reader Endpoint**: Load-balances across all read replicas.
- **Custom Endpoint**: Route traffic to specific instances (e.g., high-memory instances for analytics).

**Backups:**
- **Automatic**: Continuous and incremental — PITR within 1–35 days. Default retention is **1 day**. **Cannot be disabled** (unlike RDS). No performance impact or interruption while backup data is being written. Stored in S3.
- **Manual Snapshot**: Take a snapshot to retain data **beyond the retention period** — stored until explicitly deleted. Use to create a new DB cluster from a point-in-time copy.
- **Restore options**: From snapshot or using **Percona XtraBackup** (from S3).

**Aurora Serverless:**
- On-demand, auto-scaling — automatically starts, shuts down, and scales based on load.
- Accessed via a proxy endpoint managed by AWS.
- Use case: Infrequent or unpredictable workloads.

**Aurora Global Database:**
- Single Aurora DB spanning multiple regions — primary (1) + secondary read-only regions (up to 10).
- Up to 16 read replicas per secondary region.
- Replication lag < 1 second. RTO < 1 minute.

**Aurora Database Cloning:**
- Creates a copy of the cluster faster than snapshot/restore.
- Uses **copy-on-write** — new cluster shares original data until changes are made, then allocates separate storage.

**Aurora Machine Learning:**
- Run ML models within SQL queries. Integrates with **SageMaker** (general ML) and **Comprehend** (sentiment analysis).

**BabelFish:**
- Allows Microsoft SQL Server applications to run on Aurora PostgreSQL without code changes — translates T-SQL to PostgreSQL.

**Encryption:**
- Same as RDS — KMS at rest (must be set at launch), SSL/TLS in transit.

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
