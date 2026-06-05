## AWS KMS (Key Management Service)

AWS KMS is a managed service to **create, store, control, and rotate encryption keys** used to protect data across AWS services. All KMS keys never leave KMS unencrypted — cryptographic operations happen inside KMS. It is regional service.

---

## KMS Key Types

**By ownership:**
- **AWS Owned Keys(free)**: Created and managed entirely by AWS — used internally by services on your behalf. Not visible in your account, no cost, no control, no audit. (e.g., SSE-S3(default S3 SSE-S3 encryption), SSE-SQS, SSE-DDB)
- **AWS Managed Keys(free)**: Created by AWS for a specific service in **your account** — visible in KMS console with alias `aws/<service>` (e.g., `aws/s3`, `aws/rds`). No cost. Automatically rotated every year. Cannot be deleted, disabled, or have policies modified.
- **Customer Managed Keys (CMK)**: Created by you — full control over key policy, rotation, disabling, deletion, and cross-account sharing. Charged **$1/key/month** + per-API-call charges.

**By key material origin:**
- **KMS-generated**: AWS generates and manages the key material inside HSMs(Hardware Security Modules) — default and recommended.
- **Imported key material**: You generate key material outside AWS and import it into KMS. You are responsible for keeping a copy. Automatic rotation is **not supported** — must rotate manually.
- **Custom Key Store (CloudHSM)**: Key material generated and stored in your **dedicated CloudHSM cluster** — KMS operations are proxied to your HSM. Highest isolation, highest cost.
  - Key material is **non-extractable** — never leaves the HSM in plaintext. All KMS cryptographic operations using these keys happen only inside your CloudHSM cluster.
  - **Immediate key removal**: Because you own and control the CloudHSM cluster, you can immediately remove key material by deleting it from the cluster — without waiting for KMS key deletion schedules (7–30 day minimum for regular CMKs).
  - **Independent audit**: Key usage is logged in your CloudHSM cluster audit logs — independently of AWS KMS and AWS CloudTrail. Useful when compliance mandates auditing beyond CloudTrail.
  - **Full control**: You manage the lifecycle of keys in your CloudHSM cluster independently of AWS KMS.

  **When to use Custom Key Store (vs regular CMK):**

  | Requirement | Custom Key Store (CloudHSM) | Regular CMK |
  |---|---|---|
  | Keys in dedicated single-tenant HSM | ✓ | ✗ (shared AWS HSM) |
  | Immediately remove key material from AWS | ✓ | ✗ (min 7-day deletion wait) |
  | Audit key usage independently of CloudTrail | ✓ | ✗ (CloudTrail only) |
  | Full key lifecycle control | ✓ | Partial |
  | AWS service integration (S3, RDS, EBS…) | ✓ | ✓ |
  | Cost | High (CloudHSM cluster + KMS) | $1/key/month |

**By key spec:**
- **Symmetric (AES-256)**: Single key used for both encrypt and decrypt — default. Used by all AWS services that integrate with KMS.
- **Asymmetric (RSA / ECC)**: Public(encrypt)/private(decrypt) key pair — public key can be downloaded and used outside AWS. Used for digital signing or encryption where the caller cannot call KMS directly.

#### NOTE: AWS KMS requests are always not free - 0.03 USD per 10,000 API requests to KMS.
---

## Envelope Encryption

**Why it exists:** KMS can directly encrypt a maximum of **4 KB** of data per API call. A real file — a database, an S3 object, an EBS volume — is far larger. You can't send a 500 MB file to KMS to encrypt it directly.

**The solution — encrypt the key, not the data:**
Instead of sending your data to KMS, you generate a small random **data key**, encrypt your large data locally with that key, then ask KMS to encrypt just the small data key. The data key easily fits within the 4 KB limit.

**Real-world analogy:** You lock your house (data) with a house key (data key). Then you put that house key inside a safe (KMS CMK encrypts the data key). Anyone who wants access must first open the safe (ask KMS to decrypt the data key), get the house key, and then unlock the house. KMS only ever touches the key — never the house itself.

```
─── ENCRYPT ──────────────────────────────────────────────────────────

  Your large file (e.g. 500 MB)
        │
        │  1. Call KMS: GenerateDataKey(CMK)
        │     KMS returns:
        │       ├─ Plaintext data key   (use this to encrypt)
        │       └─ Encrypted data key   (store this alongside the data)
        │
        │  2. Encrypt your file locally using the Plaintext data key
        │  3. Immediately discard the Plaintext data key from memory
        │
        └─► Store:  [ Encrypted file ] + [ Encrypted data key ]
            (KMS never sees your file. KMS never stores the data key.)

─── DECRYPT ──────────────────────────────────────────────────────────

  [ Encrypted file ] + [ Encrypted data key ]
        │
        │  1. Call KMS: Decrypt(Encrypted data key)
        │     KMS returns: Plaintext data key
        │
        │  2. Decrypt your file locally using the Plaintext data key
        │  3. Immediately discard the Plaintext data key from memory
        │
        └─► Original file recovered
```

**Key points:**
- KMS **never stores the data key** — it generates it, hands it to you, and forgets it.
- KMS **never sees your actual data** — only the tiny encrypted data key comes back to KMS during decrypt.
- The encrypted data key is safe to store publicly alongside your data — it's useless without KMS access.
- All AWS services (S3 SSE-KMS, EBS, RDS, Secrets Manager, etc.) use envelope encryption under the hood — this is happening automatically every time you use KMS-based encryption.

---

### Key Policy

- Every KMS key must have a **key policy** — it is a **resource-based policy** attached to the key that defines who can use and manage it.
- KMS does **not rely on IAM policies alone**. Access is primarily controlled by the key policy. IAM policies work **only if the key policy explicitly allows it**.
- By default, key policies typically include the **account root** (`arn:aws:iam::<account-id>:root`), which enables delegation of access control to IAM policies.
- **Key administrators**:
  - Can manage the key (update policy, enable/disable, delete, rotation settings).
  - Cannot use the key for cryptographic operations unless explicitly granted permissions.
- **Key users**:
  - Can perform cryptographic operations (e.g., `kms:Encrypt`, `kms:Decrypt`, `kms:GenerateDataKey`).
  - Cannot manage the key.
- To allow IAM policies to control access, the key policy must include a statement that allows the account root principal. Without this, IAM policies alone cannot grant access to the key.
- **Cross-account access** requires both:
  1. A key policy in the owning account that allows the external principal.
  2. An IAM policy in the external account that allows the required KMS actions.

---

## Key Rotation

- **AWS Managed Keys**: Automatically rotated every **year** — cannot be disabled.
- **Customer Managed Keys**: Automatic rotation every **year** (optional, must be enabled). Key material changes but the key ID and ARN stay the same — no re-encryption of existing data needed (KMS tracks old key material to decrypt old ciphertext). Can set upto 90 days to 2560 days rotation period.
- **Imported key material**: Automatic rotation **not supported** — must manually rotate by creating a new key and re-pointing the alias.
- Old key material is **retained indefinitely** after rotation — needed to decrypt data encrypted with the previous version.

---

## Key Deletion

- Customer managed keys can be **scheduled for deletion** — minimum waiting period of **7 days**, maximum **30 days**.
- During the waiting period, the key is disabled — all encrypt/decrypt operations fail immediately, giving you time to detect any applications still using the key.
- **Cannot be undone after deletion** — any data encrypted with the key and no backup copy is permanently unrecoverable.
- Best practice: **disable the key first**, monitor CloudTrail for any decrypt attempts, then schedule deletion after confirming no active usage.

---

## Multi-Region Keys

Multi-Region keys are KMS keys in different AWS Regions that can be used **interchangeably** — same key material, same key ID across regions. This lets you encrypt data in one region and decrypt it in another **without re-encrypting or making a cross-Region KMS call**.

```
us-east-1 (primary)          eu-west-1 (replica)
─────────────────────         ──────────────────────
mrk-abc123 (key ID)   ════▶   mrk-abc123 (same key ID)
same key material             same key material
different ARN                 different ARN
own key policy                own key policy
own billing                   own billing
```

**Key properties:**
- **Same key ID and key material** across all replicas — ciphertext encrypted in one region can be decrypted in another without KMS calls crossing regions.
- Each replica is managed **independently** — separate key policies, aliases, tags, and billing.
- **Not auto-replicated** — you must explicitly create replicas per region. AWS never automatically creates or replicates multi-Region keys.
- **Cannot convert** a single-Region key to a multi-Region key — and vice versa. Existing data encrypted with a single-Region key keeps its data residency properties permanently.

**Use cases:**
- **Disaster recovery**: Decrypt data in a backup region without interruption if the primary region goes down.
- **Global data management**: Globally distributed apps encrypt/decrypt with the same key ID in any region — no latency from cross-region KMS calls.
- **Distributed signing**: Generate identical digital signatures across regions using asymmetric multi-region keys.
- **Active-active apps**: Workloads spanning multiple regions share the same key material for concurrent encrypt/decrypt.

**Critical caveat — AWS service-managed encryption:**
> Most AWS services that integrate with KMS (SSE at rest, digital signatures) **treat multi-Region keys as single-Region keys**. They may re-wrap or re-encrypt data when it moves between regions.
>
> Example: **S3 cross-region replication** decrypts the object in the source region and re-encrypts it with a KMS key in the **destination region** — even if the source object was protected by a multi-Region key.

This means multi-Region keys give you true cross-region portability **only when your application code calls KMS directly** (e.g., client-side encryption with the AWS Encryption SDK), not when relying on service-managed SSE.

**EBS snapshot copy (no multi-region key):**
- Region A: Snapshot of EBS volume encrypted with a regional KMS key.
- Region B: Copy snapshot → must specify a KMS key in Region B — AWS does not select one.
- Region B: Snapshot is **re-encrypted** using the Region B key during copy.

**DynamoDB backup copy (with multi-region key):**
- Region A: Backup encrypted with a multi-Region KMS key.
- Region B: Restore backup → same multi-Region key ID resolves in Region B.
- **No re-encryption needed** — ciphertext is portable across regions.

---

## KMS API — Key Calls

- `kms:Encrypt` — encrypt up to 4 KB of data directly.
- `kms:Decrypt` — decrypt ciphertext (KMS identifies the key from the ciphertext metadata).
- `kms:GenerateDataKey` — returns a plaintext + encrypted data key for envelope encryption.
- `kms:GenerateDataKeyWithoutPlaintext` — returns only the encrypted data key (for deferred encryption).
- `kms:ReEncrypt` — decrypt ciphertext with one key and re-encrypt with another, without exposing plaintext to the caller.
- All API calls are logged in **CloudTrail** — who called, which key, when, from which IP.

---

## Encryption in AWS

Encryption in AWS applies at two levels — **at rest** (stored data) and **in transit** (data moving over the network). At rest encryption has two approaches: server-side and client-side.

---

## Server-Side Encryption (SSE)

The server (AWS) encrypts data **after receiving it** and decrypts it **before returning it**. You don't manage the encryption cycle — AWS handles it transparently.

### SSE-S3 (S3-Managed Keys)
- AWS generates and manages the encryption keys entirely — no visibility or control over the keys.
- Uses **AES-256** algorithm.
- Enabled by **default** on all new S3 objects.
- No additional cost — included with S3.
- Request header: `"x-amz-server-side-encryption": "AES256"`
- **How it works under the hood**: Each object is encrypted with its own **unique data key**. That data key is then encrypted with a **root key** managed by S3, which AWS regularly rotates. So even if one object's key were exposed, other objects are unaffected — every object has a different key.

### SSE-KMS (KMS-Managed Keys)
- Keys managed in **AWS KMS** — you can create, rotate, disable, and **audit key usage via CloudTrail**.
- Provides an audit trail: every encryption/decryption call is logged in CloudTrail.
- Request header: `"x-amz-server-side-encryption": "aws:kms"`
- **Limitation**: Each upload (encrypt) and download (decrypt) makes a KMS API call — counts against the KMS request quota (5,500–30,000 req/s depending on region). High-throughput workloads can hit this limit; request a quota increase via Service Quotas Console.

### SSE-DSSE-KMS (Dual-Layer SSE with KMS)
- Two independent layers of KMS encryption applied to the same object — satisfies compliance requirements that mandate double encryption.
- Both layers use customer-managed KMS keys.
- It uses 2 encryption methods(SSE-KMS + SSE-KMS or SSE-S3) to encrypt the same object, providing an additional layer of security.
- Higher cost due to two KMS API calls per operation.

### SSE-C (Customer-Provided Keys)
- You provide the encryption key with every request — AWS uses it to encrypt/decrypt, then **discards it immediately**. AWS never stores the key.
- **HTTPS is mandatory** — the key is sent in the request header and must be protected in transit.
- Not supported via the S3 console — CLI or SDK only.
- Request headers: `"x-amz-server-side-encryption-customer-algorithm": "AES256"` + key + MD5 of key.

**SSE Comparison:**

| | SSE-S3 | SSE-KMS | SSE-DSSE-KMS | SSE-C |
|---|---|---|---|---|
| **Key managed by** | AWS (S3) | AWS KMS | AWS KMS (×2) | You (per request) |
| **Key visibility** | None | Full (CloudTrail) | Full (CloudTrail) | Full (you own it) |
| **Audit trail** | No | Yes | Yes | No (on your side) |
| **Console support** | Yes | Yes | Yes | No (CLI/SDK only) |
| **HTTPS required** | No | No | No | **Yes** |
| **Extra cost** | No | KMS API charges | 2× KMS API charges | No |

---

## Client-Side Encryption (CSE)

You encrypt data **before sending it to AWS** — AWS only ever sees and stores the already-encrypted bytes. You own and manage the full encryption cycle.

- Use **AWS Encryption SDK** or AWS SDKs to perform client-side encryption.
- Decryption also happens on the client — AWS cannot read your data at any point.
- Key can be a **KMS-managed key** (AWS stores the key, you manage access) or a **client-managed key** (fully self-managed, no AWS involvement).
- Use case: regulatory requirements where data must be encrypted before leaving your environment, or zero-trust models where even AWS should not have access to plaintext.

**SSE vs CSE:**

| | Server-Side Encryption (SSE) | Client-Side Encryption (CSE) |
|---|---|---|
| **Where encrypted** | On AWS servers after upload | On your machine before upload |
| **Who manages decrypt** | AWS (transparent to caller) | Your application |
| **AWS sees plaintext** | Briefly during encryption | Never |
| **Complexity** | Low — AWS handles it | Higher — you manage the cycle |
| **Best for** | Standard compliance, ease of use | Zero-trust, strict data sovereignty |

---

## Encryption in Transit (In-Flight)

Protects data **moving over the network** between clients and AWS services — prevents interception (man-in-the-middle attacks).

- Implemented via **SSL/TLS** (HTTPS).
- All AWS service endpoints support HTTPS. Most enforce it by default; some allow HTTP fallback.
- **Enforce HTTPS on S3** using a bucket policy with `aws:SecureTransport: false` deny condition:
```json
{
  "Effect": "Deny",
  "Principal": "*",
  "Action": "s3:*",
  "Resource": "arn:aws:s3:::example-bucket/*",
  "Condition": {
    "Bool": { "aws:SecureTransport": "false" }
  }
}
```
- **SSE-C requires HTTPS** — the encryption key is sent in the request header and must be protected.
- Services like **RDS, Aurora, Redshift, ElastiCache** use SSL/TLS certificates for in-transit encryption — clients must connect with SSL enabled.

**At Rest vs In Transit:**

| | Encryption at Rest | Encryption in Transit |
|---|---|---|
| **Protects against** | Unauthorized access to stored data (disk theft, S3 breach) | Network interception, MITM attacks |
| **Mechanism** | SSE (S3, EBS, RDS) or CSE | SSL/TLS (HTTPS) |
| **Managed by** | AWS (SSE) or you (CSE) | AWS (certificates) + you (enforce HTTPS) |
| **Always on** | Depends on service/config | HTTPS available on all services |
