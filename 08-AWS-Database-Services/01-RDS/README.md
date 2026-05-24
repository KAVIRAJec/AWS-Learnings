## Amazon RDS (Relational Database Service)

Amazon RDS is a managed relational database service supporting **MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, and Amazon Aurora**.

**Key Concepts:**
- **Automated Backups**: Transaction logs backed up every 5 minutes — point-in-time recovery within 1–35 days retention. Set to 0 to disable.
- **Manual Snapshots**: Stored in S3, retained indefinitely — used for long-term backup.
- **Storage Auto Scaling**: Automatically expands storage when free space < 10%, low for 5 mins, and 6 hours since last modification. You set a max threshold.
- **Read Replicas**: Up to 15 read replicas per instance — asynchronous replication for read-heavy workloads. Supports within-AZ, cross-AZ, cross-region. Free data transfer within the same region, charged cross-region.
- **Multi-AZ**: Synchronous standby replica in a different AZ for high availability and failover — not for scaling. To convert single-AZ to Multi-AZ: click modify → no downtime (AWS takes a snapshot, creates standby, establishes sync).
- **Authentication**: Supports IAM database authentication (MySQL, PostgreSQL) — generates temporary auth tokens valid for 15 minutes. Eliminates the need to store DB credentials in your app.

**RDS Encryption:**
- **At rest**: KMS encryption — must be enabled at launch. If master is unencrypted, replicas cannot be encrypted. To encrypt an existing unencrypted DB: snapshot → copy with encryption → restore.
- **In transit**: SSL/TLS using AWS TLS root certificates.

**RDS Proxy:**
- Fully managed, serverless, highly available (Multi-AZ) database proxy.
- Pools and shares DB connections — prevents connection exhaustion, reduces failover time by ~66%.
- Only accessible from within a VPC (not public). Enforces IAM auth + stores credentials in Secrets Manager.
- Supports MySQL, PostgreSQL, MariaDB, SQL Server, and Aurora.

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

**RDS Custom (Oracle & SQL Server):**
- Managed RDS with access to the underlying OS and DB engine via SSH or SSM Session Manager.
- Deactivate Automation Mode before making custom changes to prevent AWS from overriding them.
