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
- **Automatic**: Transaction logs every 5 minutes — PITR within 1–35 days. **Cannot be disabled**.
- **Manual Snapshot**: Stored in S3, retained until deleted.
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
