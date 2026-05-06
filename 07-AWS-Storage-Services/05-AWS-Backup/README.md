## AWS Backup

**AWS Backup** is a fully managed, centralized backup service that automates and consolidates backups across AWS services — instead of managing backups separately per service (RDS snapshots, EBS snapshots, DynamoDB backups), you define backup policies in one place.

- Supports backup across **multiple AWS services, accounts, and regions** from a single console.
- Policy-driven — define backup frequency, retention, and lifecycle in a **Backup Plan**.
- Enforces compliance — immutable backups with **Vault Lock (WORM)**.

---

## Key Concepts

**Backup Plan:**
- A policy that defines *when* to back up, *how long* to retain, and *where* to store backups.
- Contains one or more **Backup Rules** — each rule specifies a schedule, retention period, and target vault.
- Assigned to **resources** via tags or resource ARNs.

**Backup Rule:**
- Schedule: cron expression or frequency (every 12 hours, daily, weekly, monthly).
- **Backup Window**: Time window during which the backup job starts.
- **Retention Period**: How long to keep the backup before auto-deletion.
- **Lifecycle**: Move backup to cold storage after N days → delete after N days (cost optimization).

**Backup Vault:**
- Encrypted container where recovery points are stored.
- Access controlled via **resource-based policy** (who can access, restore, or delete).
- Default vault created automatically; you can create custom vaults per region, per team, per compliance requirement.

**Recovery Point:**
- A snapshot/backup of a resource at a specific point in time — the actual backed-up data stored in the vault.
- Each recovery point has metadata: resource ARN, creation time, size, status.

**Backup Job:**
- The execution of a backup rule for a specific resource — creates a recovery point in the vault.

---

## Supported Services

| Category | Services |
|---|---|
| **Compute** | EC2 (AMI + EBS volumes) |
| **Storage** | EBS, S3, EFS, FSx (Windows, Lustre, NetApp, OpenZFS) |
| **Database** | RDS, Aurora, DynamoDB, DocumentDB, Neptune |
| **Hybrid** | VMware workloads (on-premises via AWS Backup Gateway) |

---

## Backup Lifecycle (Cost Optimization)

Backups can automatically transition between storage tiers to reduce cost:

```
Backup created (warm storage)
    │
    │  after 30 days → transition to cold storage (lower cost, higher retrieval cost)
    ▼
Cold Storage
    │
    │  after 365 days → delete automatically
    ▼
Deleted
```

- **Warm storage**: Frequent access, standard cost.
- **Cold storage**: Infrequent access, ~50% cheaper — minimum 90-day cold storage duration required.

---

## Cross-Region & Cross-Account Backup

**Cross-Region:**
- Copy recovery points to a different AWS region — for disaster recovery.
- Defined within the Backup Rule: primary vault → copy to vault in another region.

```
Backup Plan (us-east-1)
    │  backup job runs → recovery point in us-east-1 vault
    │  copy rule → copy to eu-west-1 vault
    ▼
Recovery point exists in both regions
```

**Cross-Account (via AWS Organizations):**
- Copy backups to a separate AWS account — isolated from the source account.
- If the source account is compromised (ransomware, accidental deletion), backups in another account are safe.
- Requires AWS Organizations and delegated Backup administrator.

```
Account A (production) ──► backup ──► Account B (backup account, isolated)
```

---

## Backup Vault Lock (WORM)

**Vault Lock** enforces a **Write Once Read Many (WORM)** policy on a vault — backups cannot be deleted or altered for the lock duration, even by the root account.

- **Compliance mode**: Once set, the lock **cannot be removed** — not even by AWS. Meets regulatory requirements (SEC 17a-4, HIPAA, GDPR).
- **Governance mode**: Lock can be removed by privileged IAM users — less strict, for internal policy enforcement.
- Set a **minimum** and **maximum** retention period — backup jobs that violate these are rejected.

---

## Tag-Based Backup Assignment

Instead of listing every resource ARN, assign backup plans using **resource tags**:

```
Tag: Backup = "daily-prod"
    └──► Any resource with this tag is automatically enrolled in the backup plan
```

New resources with the tag are automatically backed up — no manual assignment needed.

---

## AWS Backup vs Service-Native Backups

| | AWS Backup | Service-Native (RDS snapshots, EBS snapshots) |
|---|---|---|
| **Management** | Centralized — one place for all services | Per-service — RDS console, EBS console, etc. |
| **Cross-service policy** | Yes — one plan covers EC2, RDS, DynamoDB | No — each service needs its own policy |
| **Cross-account copy** | Yes | Limited (EBS/RDS support, but manual) |
| **Vault Lock (WORM)** | Yes | No |
| **Compliance reporting** | Yes — built-in compliance dashboard | No |
| **Tag-based assignment** | Yes | No |
| **Cost** | Same underlying storage cost | Same underlying storage cost |
