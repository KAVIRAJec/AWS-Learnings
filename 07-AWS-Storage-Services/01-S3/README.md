## AWS S3 (Simple Storage Service)

AWS S3 is an object storage service that offers high durability, availability, and scalability. Designed to store and retrieve any amount of data from anywhere on the web. Used for backup, archiving, big data analytics, and content distribution.

**Key Concepts:**
- **Bucket**: A container for storing objects. Globally unique name across all AWS accounts. Regionally scoped.
- **Object**: The fundamental entity stored in S3 — data, metadata, and a unique key. Maximum object size: **5 TB** (files above 5 GB must use multipart upload).
- **Key**: The unique identifier for an object — full path including prefixes and filename (e.g., `s3://my-bucket/prefix/object_name`). No real directories — prefixes simulate folder structure in the console.
- **Metadata**: Key-value pairs attached to an object — system metadata (content-type, size) and user-defined metadata.

**Versioning:**
- Keeps multiple versions of an object in the same bucket — useful for recovery and rollback.
- Objects without versioning enabled have a version ID of `null`.
- Deleting a versioned object creates a **delete marker** — the object is recoverable by removing the marker.
- Suspending versioning stops creating new versions but retains all existing ones.

**Lifecycle Policies:**
Rules that automate transitions and deletions. Can be scoped to a prefix or object tags.
- **Transition**: Move objects to a cheaper storage class after N days.
- **Expiration**: Permanently delete objects after N days.
- **Noncurrent Version Expiration**: Delete old versions after N days.
- **Incomplete Multipart Upload**: Auto-delete stale incomplete uploads after N days.

**Static Website Hosting:**
- S3 can serve HTML, CSS, and JavaScript directly from a bucket as a static website.
- Bucket must be **publicly accessible** — Block Public Access must be disabled and a bucket policy allowing `s3:GetObject` for `"Principal": "*"` must be attached.
- Enable via S3 console → Properties → Static website hosting → specify index and error documents.
- S3 generates a **website endpoint URL**: `http://<bucket-name>.s3-website-<region>.amazonaws.com`

**Routing Route 53 to an S3 static website — prerequisites:**
1. **Registered domain name** — you must own the domain (via Route 53 or any registrar).
2. **Bucket name must exactly match the domain name** — if your domain is `www.example.com`, the bucket must be named `www.example.com`. Route 53 uses the bucket name to resolve the correct S3 website endpoint.
3. **Static website hosting must be enabled** on the bucket.
4. In Route 53, create an **A record (Alias)** pointing to the S3 website endpoint.
site from S3 directly is not cross-origin.

**Security (overview):**
- **User-Based**: IAM policies for users and roles.
- **Resource-Based**: Bucket policies (JSON, cross-account, max **20 KB** in size), ACLs (legacy, can be disabled), Object ACLs.
- **Access Rule**: An IAM principal can access an S3 object if the IAM policy **OR** the resource policy allows it — AND there is no explicit deny in either.
- **Block Public Access**: Enabled by default on all buckets — additional layer on top of bucket policies and ACLs.
- **Gateway Endpoint**: Access S3 from private subnets without going through the internet.

**Sample Bucket Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

---

## Topics

| # | File | Contents |
|---|---|---|
| 1 | [Storage Classes](./01-Storage-Classes.md) | All 7 storage classes, S3 Analytics, pricing, storage class ordering |
| 2 | [Replication](./02-Replication.md) | CRR, SRR, RTC, Batch Replication, replication encryption (SSE-KMS) |
| 3 | [Performance](./03-Performance.md) | Baseline performance, Multipart Upload, Transfer Acceleration, Byte-Range Fetches, Batch Operations |
| 4 | [Security](./04-Security.md) | Encryption (SSE-S3/KMS/DKMS/C, client-side, in-transit), CORS, MFA Delete, Access Logs, Presigned URLs, Glacier Vault Lock, Object Lock, Access Points |
| 5 | [Storage Lens](./05-Storage-Lens.md) | Organization-wide visibility, metric categories, Free vs Advanced metrics |
| 6 | [Event Notifications](./06-Event-Notifications.md) | SNS/SQS/Lambda direct notifications, EventBridge integration |
