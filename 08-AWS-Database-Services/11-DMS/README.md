## AWS DMS (Database Migration Service)

**AWS DMS** is a managed service that migrates databases to AWS quickly and securely — the source database remains fully operational during migration, minimizing downtime.

- Supports **homogeneous** (same engine: MySQL → MySQL) and **heterogeneous** (different engine: Oracle → Aurora) migrations.
- Handles **one-time migration** (move once) or **continuous replication** (ongoing CDC sync).
- Works with relational databases, data warehouses, NoSQL databases, and S3.

---

## How It Works

```
Source DB                    DMS Replication Instance              Target DB
(on-premises / EC2 / RDS)         (EC2 in VPC)               (RDS / Aurora / S3 etc.)
        │                               │                               │
        │──── reads data ──────────────►│                               │
        │                               │──── writes data ─────────────►│
        │                               │                               │
        │  (source stays live during migration — no downtime)           │
```

DMS runs a **Replication Instance** — an EC2 instance you provision — that reads from the source, converts the data if needed, and writes to the target.

---

## Migration Types

**Full Load:**
- One-time bulk copy of all existing data from source to target.
- Source keeps running; no ongoing sync after initial load.
- Use when you can afford a brief cutover window.

**Full Load + CDC (Change Data Capture):**
- Bulk loads existing data first, then continuously captures and applies ongoing changes from the source.
- Keeps source and target in sync — cut over to target when ready with near-zero downtime.
- CDC reads from the database's transaction log (binlog for MySQL, redo log for Oracle, WAL for PostgreSQL).

**CDC Only:**
- Skips the initial load — assumes target already has data.
- Only replicates ongoing changes (inserts, updates, deletes).
- Used for ongoing replication or when you pre-populated the target another way.

```
Full Load + CDC timeline:

Source DB ──────────────────────────────────────────────► (stays live)
              │                        │
         Full Load               CDC starts
         (bulk copy)         (captures ongoing changes)
              │                        │
Target DB ────┼────────────────────────┼──────────────────► (in sync)
                                       │
                               Cutover when ready
                               (update app connection string)
```

---

## Homogeneous vs Heterogeneous Migration

**Homogeneous** — same database engine on both ends:
```
MySQL → MySQL (RDS)
PostgreSQL → PostgreSQL (Aurora)
Oracle → Oracle (RDS)
```
- Schema and data types are compatible — DMS migrates directly.
- No schema conversion needed.

**Heterogeneous** — different database engine:
```
Oracle → Aurora PostgreSQL
SQL Server → MySQL (RDS)
MongoDB → DynamoDB
```
- Schema, data types, and stored procedures differ between engines.
- Use **AWS Schema Conversion Tool (SCT)** first to convert the schema and application code, then use DMS to migrate the data.

```
Heterogeneous migration flow:

Step 1: AWS SCT
  Source schema (Oracle) ──► Converts ──► Target schema (Aurora PostgreSQL)
  Also converts: stored procedures, views, functions, triggers

Step 2: AWS DMS
  Source data (Oracle) ──► Migrates data ──► Target (Aurora PostgreSQL)
```

---

## Supported Sources and Targets

**Sources:**
- On-premises or EC2: Oracle, SQL Server, MySQL, PostgreSQL, MariaDB, MongoDB, SAP
- AWS: RDS, Aurora, S3, DynamoDB

**Targets:**
- AWS databases: RDS, Aurora, Redshift, DynamoDB, DocumentDB
- Storage: S3 (as CSV or Parquet — useful for data lake ingestion)
- Streaming: Kinesis Data Streams, Amazon MSK (Kafka)
- On-premises databases (for replication back)

---

## AWS SCT (Schema Conversion Tool)

**AWS Schema Conversion Tool** is a desktop/CLI tool that automatically converts:
- Database schema (tables, indexes, constraints)
- Stored procedures, functions, triggers, views
- Application SQL code embedded in app source files

SCT generates a **migration assessment report** — shows what it can convert automatically and what needs manual review (complex PL/SQL, proprietary features).

> SCT is only needed for **heterogeneous** migrations. For same-engine migrations, schema is already compatible.

---

## Replication Instance — Multi-AZ

The **Replication Instance** is the EC2 that runs DMS tasks. By default it runs in a single AZ — if that AZ fails, the migration/replication stops.

Enabling **Multi-AZ** on the replication instance provisions a **standby replica** in a different AZ — AWS automatically fails over to it if the primary instance fails or the AZ has an outage.

```
AZ-A                              AZ-B
┌─────────────────────┐          ┌─────────────────────┐
│ Replication Instance│  sync    │ Standby Replica     │
│ (primary, active)   │ ───────► │ (passive, warm)     │
└─────────────────────┘          └─────────────────────┘
         │
         │  If AZ-A fails → automatic failover → AZ-B takes over
         │  ongoing CDC replication continues without manual intervention
```

- Failover is **automatic** — no manual action needed.
- Adds **redundancy for long-running CDC replications** — critical for continuous replication tasks that run for days/weeks.
- Increases cost — two replication instances running instead of one.
- Recommended for **production CDC tasks**; not needed for short one-time full-load migrations.

---
