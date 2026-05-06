## AWS Application Migration Service (AWS MGN)

**AWS Application Migration Service (MGN)** is a lift-and-shift migration service that replicates your source servers (on-premises, other clouds, or other AWS regions) to AWS as EC2 instances — with minimal downtime and no application changes needed.

- Formerly known as **CloudEndure Migration**.
- Works for **any OS, any application, any database** — no refactoring required.
- Continuously replicates block-level data to a low-cost staging area until cutover.
- **RPO: seconds, RTO: minutes** — similar to Elastic DRS but for one-time migration, not ongoing DR.

---

## How It Works

```
Source Server (on-premises / other cloud)
    │
    │  1. Install Replication Agent (lightweight)
    ▼
Replication Agent
    │  continuously replicates block-level disk changes
    │  encrypted in transit (TLS)
    ▼
Staging Area (target AWS region)
    │  low-cost EBS volumes + small replication servers
    │  (runs cheap 24/7 — not production-sized)
    ▼
On cutover:
    │  2. MGN converts staging volumes → EC2 instance volumes
    │  3. Launches EC2 using pre-configured Launch Template
    ▼
EC2 Instance (target AWS region)
    └──► redirect app traffic here → migration complete
```

---

## Key Concepts

**Replication Agent:**
- Installed on each source server (Windows/Linux).
- Captures all block-level disk changes continuously — keeps staging area in sync with source.
- Minimal performance impact on source server.

**Staging Area:**
- Low-cost EC2 + EBS in the target region — holds replicated data.
- Not accessible to end users — only used during cutover to launch the migrated instance.
- You pay only for cheap storage + small replication servers while staging.

**Launch Template:**
- Pre-configure the target EC2: instance type, subnet, security groups, IAM role, tags.
- MGN uses this during cutover to launch the correctly sized and configured EC2 instance.

**Test Cutover (Migration Drill):**
- Launch a test instance from the staging area without stopping source.
- Verify the migrated server works correctly in AWS before the real cutover.
- Isolated from production — test instance does not affect source or staging replication.

**Cutover:**
- Final step — stop the source server, let replication catch up fully, launch the production EC2.
- Update DNS / load balancer to point to the new EC2.
- **Downtime**: Minutes (replication catch-up + instance launch).

---

## Migration Flow

```
Phase 1 — Replication (days/weeks, source stays live):
  Source server ──► Agent replicates changes ──► Staging Area (syncing continuously)

Phase 2 — Test Cutover (verify before committing):
  Staging Area ──► launch test EC2 ──► validate app works ──► terminate test instance

Phase 3 — Cutover (final migration):
  1. Stop writes on source
  2. Wait for final sync (seconds)
  3. MGN launches production EC2 from staging
  4. Update DNS/ALB → new EC2
  5. Source server decommissioned
```

---

## MGN vs Elastic Disaster Recovery (DRS)

Both use the same underlying replication technology — agent-based, continuous block-level replication with a staging area. The difference is the **intent**:

| | AWS MGN | AWS Elastic DRS |
|---|---|---|
| **Purpose** | One-time migration to AWS | Ongoing DR — recover if source fails |
| **Source** | On-premises / other cloud | On-premises / other cloud / AWS region |
| **Replication** | Until cutover, then stop | Continuous indefinitely |
| **Cutover** | Permanent — source decommissioned | Failover — source stays, failback supported |
| **Failback** | Not applicable | Built-in |
| **Use case** | Migrate servers to AWS | Keep AWS as DR target for on-premises |

---

## MGN vs DMS

| | AWS MGN | AWS DMS |
|---|---|---|
| **What it migrates** | Entire servers (OS + apps + data) | Databases only |
| **Method** | Block-level replication (agent) | Row-level replication (CDC / SQL) |
| **Heterogeneous** | No — same OS/app, different infrastructure | Yes — Oracle → Aurora, SQL Server → MySQL |
| **Schema conversion** | Not needed | SCT needed for heterogeneous |
| **Use case** | Lift-and-shift whole server | Database engine change or DB-only migration |
