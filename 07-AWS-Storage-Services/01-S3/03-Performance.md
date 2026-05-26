## S3 Performance

---

### Baseline Performance

S3 automatically scales to high request rates with **100–200ms latency**.

Per prefix in a bucket:
- **3,500 PUT / COPY / POST / DELETE** requests per second
- **5,500 GET / HEAD** requests per second

Using **multiple prefixes** multiplies throughput linearly:
- 2 prefixes → **7,000 PUT/s** and **11,000 GET/s**
- 4 prefixes → **14,000 PUT/s** and **22,000 GET/s**

---

### S3 Multipart Upload

Splits a large object into parts and uploads them in parallel.

- **Recommended** for files > 100 MB.
- **Required** for files > 5 GB.
- Each part can be uploaded independently — a failed part only needs to be retried, not the entire file.
- Parts are reassembled by S3 after all parts are uploaded.
- Incomplete multipart uploads accumulate storage cost — use **S3 Lifecycle Policy** to auto-delete them after a set number of days.

---

### S3 Transfer Acceleration *(Write — Uploads)*

Speeds up uploads to S3 by routing data through **CloudFront edge locations**.

```
Client (India)
    │
    │  uploads to nearest CloudFront edge (fast public network)
    ▼
CloudFront Edge Location (Mumbai)
    │
    │  transferred over AWS backbone (private, fast)
    ▼
S3 Bucket (us-east-1)
```

- Useful for transferring large files over long geographical distances.
- Compatible with multipart upload.
- Small additional cost per GB transferred.

---

### S3 Byte-Range Fetches *(Read — Downloads)*

Downloads a file in parallel by requesting specific byte ranges simultaneously.

- Fetch only the portion of a file you need (e.g., just the header of a large file).
- Multiple byte ranges downloaded in parallel → faster overall download.
- If one range download fails, only that range needs to be retried.
- **Use case**: Large file downloads, partial reads (e.g., reading metadata from the first N bytes of a CSV/Parquet file).

---

### S3 Batch Operations

Perform bulk operations on a large number of existing S3 objects with a **single request**.

- A **job** consists of: a list of objects (from S3 Inventory or custom CSV) + an action to perform.
- Supported operations: Copy, Tag, Delete, Restore from Glacier, Invoke Lambda, ACL changes, Replicate, Encrypt unencrypted objects.
- Can use **S3 Inventory** or **Amazon Athena** to filter/identify the target objects.
- Tracks progress, sends notifications, and generates a completion report.
- **Use case**: Retroactively encrypt all unencrypted objects, bulk-tag objects, copy objects between buckets.
