## S3 Security

---

### Cross-Account Object Ownership

By default, an S3 object is **owned by the AWS account that uploaded it** — not the bucket owner.

This means: if Redshift (Account B) runs `UNLOAD` and writes files into a bucket owned by Account A, Account A cannot read those files by default — even though it owns the bucket.

**Fix — Cross-Account IAM Role Chain:**

```
Account A (bucket owner)          Account B (Redshift cluster)
─────────────────────────         ────────────────────────────
Bucket Role                       Cluster Role
  - S3 read/write on bucket         - sts:AssumeRole → Bucket Role
  - Trust: allows Cluster Role
```

1. **Account A** creates a **Bucket Role** with S3 permissions on the bucket + a trust policy allowing Cluster Role to assume it.
2. **Account B** creates a **Cluster Role** with `sts:AssumeRole` permission to assume the Bucket Role.
3. Redshift runs `UNLOAD` using both roles — Cluster Role assumes Bucket Role to write the files, making Account A the object owner.

> This cross-account role chain does **not** work if the bucket or Redshift cluster uses **SSE-KMS** encryption — KMS adds key policy complexity that this pattern doesn't cover.

**S3 Object Ownership setting (simpler alternative):**
Enable **Bucket Owner Enforced** on the bucket — all objects written to the bucket automatically transfer ownership to the bucket owner, regardless of who uploaded them. ACLs are disabled in this mode.

---

### S3 Encryption

Bucket policies are evaluated **before** encryption settings.

#### Server-Side Encryption (SSE)

**SSE-S3 (S3 Managed Keys)**
- Default encryption for all new objects.
- S3 manages the keys entirely — no user involvement.
- Uses **AES-256** encryption.
- Header required on upload: `"x-amz-server-side-encryption": "AES256"`

**SSE-KMS (AWS KMS Managed Keys)**
- Keys managed in AWS KMS — provides user control + audit trail in CloudTrail.
- Header required on upload: `"x-amz-server-side-encryption": "aws:kms"`
- **Limitation**: Every upload (encrypt) and download (decrypt) calls the KMS API — counts against KMS quota per second. Quota can be increased via Service Quotas Console.

**SSE-DKMS (Dual-Layer KMS)**
- Two layers of encryption — data encrypted with a KMS key, then again with a second layer.
- Higher security for compliance-sensitive workloads.

**SSE-C (Customer Provided Keys)**
- You provide the encryption key with every request — S3 does **not** store the key.
- **HTTPS is mandatory** — key is sent over the wire.
- Header: `"x-amz-server-side-encryption-customer-algorithm": "AES256"`
- Only supported via CLI/SDK — **not available in S3 Console**.

#### Client-Side Encryption
- You encrypt the data **before** uploading to S3.
- S3 stores already-encrypted bytes — has no knowledge of the key or plaintext.
- You manage the full encryption/decryption lifecycle using AWS SDKs or third-party libraries.

#### Encryption in Transit (SSL/TLS)
- S3 exposes two endpoints: **HTTP** (unencrypted) and **HTTPS** (encrypted).
- **HTTPS is mandatory for SSE-C**.
- Enforce HTTPS-only via bucket policy using `aws:SecureTransport` condition:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {
        "Bool": {
          "aws:SecureTransport": "false"
        }
      }
    }
  ]
}
```

---

### S3 CORS

Cross-Origin Resource Sharing (CORS) allows web applications in one domain to request resources from a different domain/S3 bucket.

- Configure CORS rules on the **target bucket** (the one being accessed).
- Specify the **source origin** (the bucket or domain making the request) in `AllowedOrigins`.
- Define allowed HTTP methods, headers, and preflight cache duration (`MaxAgeSeconds`).
- **Use case**: SPA hosted in bucket A fetches assets from bucket B — CORS must be enabled on bucket B.

---

### MFA Delete

Adds a second factor requirement for destructive S3 actions on **versioning-enabled** buckets.

- **Only the root user can enable/disable MFA Delete** — via AWS CLI only (not console).

**MFA required for:**
- Permanently deleting a specific object version.
- Suspending versioning on the bucket.

**MFA NOT required for:**
- Enabling versioning.
- Adding a delete marker (soft delete — object is recoverable).

---

### S3 Access Logs

Records detailed information about every request made to a bucket — requester, action, response code, bytes transferred.

- **Disabled by default**.
- Logs delivered to a **separate target bucket** in the same region.
- **Never set the source bucket as the target** — causes an infinite logging loop and unbounded storage cost.
- Log delivery is **not real-time** — may take a few hours.
- Analyze logs with **Amazon Athena**.

---

### S3 Presigned URLs

A time-limited, signed URL that grants temporary access to a **private** S3 object — without exposing AWS credentials.

- Generated by any IAM user/role that has permission to the object.
- Inherits the permissions of the generator — if the role loses access, the URL stops working immediately.

| Method | Default Expiry | Maximum Expiry |
|---|---|---|
| S3 Console | 1 hour | 12 hours |
| AWS CLI / SDK | 1 hour | 7 days (604,800s) |

**Use cases:** Temporary file download link, allow a client to upload directly to S3 without credentials.

---

### S3 Glacier Vault Lock

Enforces a **WORM (Write Once Read Many)** policy on a Glacier vault.

- Once locked, the policy **cannot be changed or deleted** — even by the root account.
- **Steps**: Initiate policy → validate and test within **24 hours** → complete lock (irreversible after this).

---

### S3 Object Lock

Provides WORM protection for **S3 objects** (not Glacier vaults). **Versioning must be enabled**.

Applied per object version.

**Retention Modes:**
- **Compliance Mode**: Nobody — including root — can delete or overwrite until retention expires. Period cannot be shortened under any circumstance.
- **Governance Mode**: Most users cannot delete/overwrite. Users with `s3:BypassGovernanceRetention` permission can override.

**Legal Hold:**
- Blocks deletion and overwrite indefinitely — **no expiry date**, no retention period.
- Set and removed independently of any retention mode — an object can have both a retention period AND a legal hold at the same time.
- Requires `s3:PutObjectLegalHold` permission to set or remove.
- Use case: Preserve evidence during a legal investigation — hold cannot expire automatically, must be explicitly removed.

**Retention Period vs Legal Hold — key differences:**

| | Retention Period | Legal Hold |
|---|---|---|
| **Expiry** | Fixed date (`retain-until-date`) | No expiry — indefinite |
| **Mode** | Compliance or Governance | No mode — always hard block |
| **Who removes it** | Nobody (Compliance) / privileged user (Governance) | Any user with `s3:PutObjectLegalHold` |
| **Can coexist** | Yes — both can be active on the same object version | Yes |
| **Use case** | Regulatory data retention (e.g., keep 7 years) | Legal hold during investigation or litigation |

**Retention — Default, Custom, and Object-Level:**

Object Lock retention can be configured at three levels — most restrictive always wins:

| Level | Where set | What it does |
|---|---|---|
| **Default Retention (Bucket-level)** | On the bucket | Auto-applies a mode + period to every new object — no per-object config needed |
| **Object-level Retention** | On a specific object version | Overrides the bucket default for that object — can extend or (in Governance) shorten |
| **Custom Retention (per upload)** | Set at PutObject time via API/SDK | Specify `retain-until-date` at upload — overrides bucket default for that object |

- **retain-until-date** — the actual date S3 checks before allowing delete/overwrite on each object version.
- In **Compliance mode**: can only extend retention, never shorten.
- In **Governance mode**: can extend or shorten with `s3:BypassGovernanceRetention`.

```
Bucket default: Governance, 1 year
    │
    ├── Object A  (no override)             → Governance, 1 year
    ├── Object B  (object-level override)   → Compliance, 7 years  ← more restrictive
    └── Object C  (custom retain-until-date)→ Governance, 90 days
```

---

### S3 Access Points

Named network endpoints attached to a bucket — each with its own access policy.

- Simplifies access management when multiple teams or applications share one bucket.
- Each team gets their own Access Point with a policy scoped to their prefix.
- Can be **VPC-restricted** — accepts requests only from within a specific VPC (no internet access).

**S3 Object Lambda Access Point:**
- Runs a Lambda function on-the-fly to transform object data **before** returning it to the caller — no need to store multiple copies or versions of the same object.
- The Lambda is triggered on S3 **GET requests** made through the Object Lambda Access Point (not the standard S3 endpoint).

**How it works:**
```
Application
    │  GET request via Object Lambda Access Point
    ▼
Lambda function invoked
    │  receives: bucket name + object key (identifies which object to fetch)
    │  fetches the original object from S3
    │  applies transformation (filter, redact, convert)
    ▼
Transformed data returned to the application
(original object in S3 is unchanged)
```

**What the Lambda uses to process the request:**
- **Bucket name + object key** — these uniquely identify the specific object to retrieve and transform.

**Use cases:**
- Filter rows from a large CSV before returning it (only return columns/rows the caller needs)
- Redact PII (SSNs, credit card numbers) from documents
- Convert data format (XML → JSON, CSV → Parquet) on-the-fly
- Add watermarks to images
- Resize images dynamically based on the caller's request
