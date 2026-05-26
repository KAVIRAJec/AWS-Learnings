## S3 Replication

S3 supports automatic, asynchronous replication of objects between buckets.

**Prerequisites:**
- **Versioning must be enabled** on both source and destination buckets.
- Buckets can be in different AWS accounts.
- Replication can be scoped to a specific prefix or all objects.
- Proper IAM permissions must be granted.
- Only **new objects** are replicated after replication is enabled — existing objects require S3 Batch Replication.

---

### Replication Types

**Cross-Region Replication (CRR)**
- Replicates objects from a bucket in one region to a bucket in a different region.
- **Use case**: Compliance requirements, low-latency access from multiple regions, cross-account replication.

**Same-Region Replication (SRR)**
- Replicates objects within the same region.
- **Use case**: Log aggregation across accounts, live replication between test and production accounts.

**Replication Time Control (RTC)**
- Provides a predictable replication SLA — replicates **99.99% of objects within 15 minutes**.
- Includes replication metrics and CloudWatch notifications.
- Available for both CRR and SRR.

---

### S3 Batch Replication

Used to replicate objects that existed **before** replication was enabled, or objects that previously failed replication.

- Runs as a one-time batch job via S3 Batch Operations.
- Can filter by prefix, tags, or object metadata.

---

### Delete Behaviour

- **Delete markers** are replicated if explicitly enabled in the replication configuration.
- **Permanent deletes** (deleting a specific version ID) are **never replicated** — this protects against accidental or malicious deletions propagating to the destination.
- No chaining of replication — if A replicates to B and B replicates to C, objects from A do **not** automatically replicate to C.

---

### Replication Encryption

| Encryption Type | Replicated by default? | Notes |
|---|---|---|
| Unencrypted | Yes | — |
| SSE-S3 | Yes | — |
| SSE-C | Yes | — |
| SSE-KMS | No — must be explicitly configured | Must specify the destination KMS key in replication config |

**For SSE-KMS replication:**
- Define the destination KMS key in the replication configuration.
- IAM role must have:
  - `kms:Decrypt` on the source bucket's KMS key.
  - `kms:Encrypt` on the destination bucket's KMS key.
- If using **multi-region KMS keys**, AWS S3 treats them as independent keys — objects are still decrypted in the source region and re-encrypted in the destination region using the corresponding regional key.
