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

**RDS Custom (Oracle & SQL Server):**
- Managed RDS with access to the underlying OS and DB engine via SSH or SSM Session Manager.
- Deactivate Automation Mode before making custom changes to prevent AWS from overriding them.
