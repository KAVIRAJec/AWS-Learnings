## Amazon RDS (Relational Database Service)

Amazon RDS is a managed relational database service supporting **MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, and Amazon Aurora**.

**Key Concepts:**
- **Automated Backups**: Transaction logs backed up every 5 minutes — point-in-time recovery within 1–35 days retention. Set to 0 to disable.
- **Manual Snapshots**: Stored in S3, retained indefinitely — used for long-term backup.
- **Storage Auto Scaling**: Automatically expands storage when free space < 10%, low for 5 mins, and 6 hours since last modification. You set a max threshold.
- **Read Replicas**: Up to 15 read replicas per instance — asynchronous replication for read-heavy workloads. Supports within-AZ, cross-AZ, cross-region. Free data transfer within the same region, charged cross-region.
- **Multi-AZ**: Synchronous standby replica in a different AZ for high availability and failover — **not for read scaling** (standby cannot serve read requests). To convert single-AZ to Multi-AZ: click modify → no downtime (AWS takes a snapshot, creates standby, establishes sync).
  - **Replication**: **Synchronous** — every write to primary is replicated to standby before the write is acknowledged. Never asynchronous for Multi-AZ.
  - **Automatic failover**: If the primary fails (AZ failure, hardware issue, DB crash), RDS automatically promotes the standby — no manual intervention needed. The DNS endpoint stays the same; applications reconnect automatically.
  - **Automated backups**: Taken from the **standby** — no I/O suspension on the primary during the backup window. This is a key Multi-AZ benefit over single-AZ (where backups suspend I/O on primary).
  - **OS/patch maintenance order**: RDS performs OS updates in this order to minimize downtime:
    1. Patch the **standby** first
    2. Promote standby → new primary
    3. Patch the **old primary** (now the new standby)
    > This means OS maintenance causes only a brief failover, not full downtime. **Exception**: engine version upgrades shut down both primary and standby at the same time — causing actual downtime.
- **Engine version upgrades**: Require downtime — even in Multi-AZ, both the primary and standby are upgraded at the same time. Downtime duration depends on DB instance size.
- **Authentication**: Supports IAM database authentication (MySQL, PostgreSQL) — generates temporary auth tokens valid for 15 minutes. Eliminates the need to store DB credentials in your app.

**RDS Scope — Region vs AZ:**

| Feature | Scope | Notes |
|---|---|---|
| **RDS Instance** | AZ-specific | Runs in one AZ within a region |
| **Multi-AZ Standby** | Different AZ, same region | Synchronous replica — HA only, not readable |
| **Read Replica (same region)** | Can be same AZ or different AZ | Asynchronous, readable, free data transfer |
| **Read Replica (cross-region)** | Different region | Asynchronous, readable, charged data transfer |
| **Automated Backups** | Region-specific | Stored in S3 within the same region, can be copied to another region manually |
| **Manual Snapshots** | Region-specific | Stored in S3; can be copied cross-region or shared with another account |
| **RDS Proxy** | AZ-aware (Multi-AZ) | Deployed across AZs in the same region |
| **Subnet Group** | Region-specific | Spans multiple AZs within a region |

---

**RDS Encryption:**
- **At rest**: KMS encryption — must be enabled at launch. If master is unencrypted, replicas cannot be encrypted. To encrypt an existing unencrypted DB: snapshot → copy with encryption → restore.
- **In transit**: SSL/TLS using AWS TLS root certificates.

**RDS Proxy:**
- Fully managed, serverless, highly available (Multi-AZ) database proxy.
- Pools and shares DB connections — prevents connection exhaustion, reduces failover time by ~66%.
- Only accessible from within a VPC (not public). Enforces IAM auth + stores credentials in Secrets Manager.
- Supports MySQL, PostgreSQL, MariaDB, SQL Server, and Aurora.

**RDS Event Subscriptions:**
- Subscribe to **infrastructure-level** RDS events and receive notifications via **SNS** — e.g., instance failover, backup completed, low storage, DB instance restarted, parameter group change.
- **Does NOT capture data changes** (INSERT, UPDATE, DELETE) — RDS event subscriptions only track operational/infrastructure events, not SQL-level activity.
- Event categories: `availability`, `backup`, `configuration change`, `deletion`, `failover`, `failure`, `maintenance`, `notification`, `recovery`, `restoration`.

**Exposing RDS to Another VPC / Partner via PrivateLink:**

RDS cannot be directly exposed through AWS PrivateLink — PrivateLink only works with NLB endpoints. So to securely share your RDS with an external account or partner **without VPC Peering**, use this pattern:

```
Partner VPC                        Your VPC
───────────                        ────────
Interface Endpoint (PrivateLink)
        │                          NLB  (forwards TCP on port 3306/5432)
        │ ──── private traffic ──▶  │
                                   RDS Proxy  (pools connections, IAM auth)
                                    │
                                   RDS Instance (never directly exposed)
```

**Why this way:**
- **RDS Proxy** sits between NLB and RDS — handles connection pooling, IAM authentication, and Secrets Manager. RDS never receives raw connections from outside.
- **NLB** gives PrivateLink a fixed TCP target to front — it forwards the DB port (3306 for MySQL, 5432 for PostgreSQL) to the proxy.
- **PrivateLink Interface Endpoint** in the partner's VPC sends traffic privately to your NLB — no internet, no VPC Peering, no CIDR overlap issue.

> The partner only sees the Interface Endpoint IP — they have no knowledge of or access to the RDS instance itself.

**RDS Enhanced Monitoring:**
- Provides **real-time OS-level metrics** collected by an agent running directly on the DB instance — not from the hypervisor.
- Captures per-process and per-thread metrics: **CPU bandwidth %, memory % consumed by each process/thread**, disk I/O, network I/O, and OS-level details not visible through the hypervisor.
- Standard CloudWatch metrics come from the **hypervisor** — they show aggregate CPU for the entire instance, not broken down by process. Enhanced Monitoring fills this gap.
- Metrics are published to **CloudWatch Logs** under the `RDSOSMetrics` log group — retained for **30 days** by default (adjustable).
- Enable per instance with a granularity of **1–60 seconds** (1s for most detailed, 60s default).
- Requires an IAM role (`AmazonRDSEnhancedMonitoringRole`) attached to the RDS instance.

| | Standard CloudWatch | Enhanced Monitoring |
|---|---|---|
| **Data source** | Hypervisor | Agent on the DB instance |
| **Granularity** | 1 min (detailed) / 5 min (basic) | 1–60 seconds |
| **Per-process metrics** | No | Yes — CPU% and MEM% per process/thread |
| **Stored in** | CloudWatch Metrics | CloudWatch Logs (`RDSOSMetrics`) |
| **Use when** | General CPU/network trends | Need to see which DB process is consuming resources |

> You do **not** have direct access to RDS instances (unlike EC2), so you cannot install a CloudWatch Agent — Enhanced Monitoring is the only way to get OS-level per-process visibility on RDS.

**RDS Custom (Oracle & SQL Server):**
- Managed RDS with access to the underlying OS and DB engine via SSH or SSM Session Manager.
- Deactivate Automation Mode before making custom changes to prevent AWS from overriding them.
