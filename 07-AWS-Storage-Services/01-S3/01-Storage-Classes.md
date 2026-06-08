## S3 Storage Classes

All storage classes provide **99.999999999% (11 9's) durability**.

---

### S3 Standard
- General-purpose storage for frequently accessed data.
- **99.99% availability** (not available ~53 minutes/year).
- No minimum storage duration, no retrieval fee.
- **Use case**: Websites, content distribution, big data analytics.

---

### S3 Intelligent-Tiering
- Automatically moves objects between tiers based on access patterns.
- Small monthly monitoring + auto-tiering fee. **No retrieval charges**.

| Tier | Condition | Type |
|---|---|---|
| Frequent Access | Default | Automatic |
| Infrequent Access | Not accessed for 30 days | Automatic |
| Archive Instant Access | Not accessed for 90 days | Automatic |
| Archive Access | 90 to 700+ days | Optional (must enable) |
| Deep Archive Access | 180 to 700+ days | Optional (must enable) |

- **Use case**: Data with unpredictable access patterns — ML datasets, analytics.

---

### S3 Standard-IA (Infrequent Access)
- Lower cost than Standard, but has a **retrieval fee**.
- **99.9% availability** (not available ~4 days/year).
- Minimum storage duration: **30 days**.
- **Use case**: Disaster recovery, backups, long-term storage.

---

### S3 One Zone-IA (Infrequent Access)
- Same as Standard-IA but stored in a **single AZ** — data lost if AZ is destroyed.
- **99.5% availability** (not available ~18 days/year). Cheaper than Standard-IA.
- Minimum storage duration: **30 days**.
- **Use case**: Secondary backup copies, data that can be easily recreated.

---

### S3 Express One Zone
- Lowest latency storage class — **single-digit millisecond** latency.
- Stored in a single AZ (high performance, lower durability trade-off).
- **99.5% availability**. Minimum storage duration: **30 days**.
- **Use case**: Latency-sensitive apps, data-intensive workloads.

---

### S3 Glacier

#### S3 Glacier Instant Retrieval
- **Millisecond** retrieval — same speed as Standard but stored at Glacier cost.
- Minimum storage duration: **90 days**.
- **Use case**: Data accessed occasionally but needs immediate access when required.

#### S3 Glacier Flexible Retrieval *(formerly S3 Glacier)*
- Minimum storage duration: **90 days**.
- **Use case**: Long-term archival, backups, compliance data.

**Retrieval tiers:**

| Tier | Time | Notes |
|---|---|---|
| **Expedited** | 1–5 minutes | Best-effort — not guaranteed under high demand unless provisioned capacity is purchased |
| **Standard** | 3–5 hours | Reliable but slow |
| **Bulk** | 5–12 hours | Cheapest (free) — slowest, not compatible with provisioned capacity |

**Provisioned Capacity:**
- Guarantees that **Expedited retrieval capacity is available when you need it** — not just best-effort.
- Without provisioned capacity, Expedited retrievals are accepted except during rare periods of unusually high demand — no SLA guarantee.
- **Required when compliance or business rules mandate retrieval under all circumstances** (e.g., surprise audits, regulatory requirements).
- Each unit of provisioned capacity provides:
  - At least **3 Expedited retrievals every 5 minutes**
  - Up to **150 MB/s** of retrieval throughput
- Only works with **Expedited** tier — Standard and Bulk retrievals are not covered by provisioned capacity.

#### S3 Glacier Deep Archive
- Lowest-cost storage class in all of AWS.
- Retrieval: **Standard** (~12 hrs), **Bulk** (~48 hrs).
- Minimum storage duration: **180 days**.
- **Use case**: Data retained for 7–10 years, regulatory compliance archives.

---

### S3 Reduced Redundancy Storage (RRS) *(Deprecated)*
- Lower durability than Standard at a lower cost. Not recommended — use other classes.

---

### S3 Analytics
- Provides recommendations for moving objects from **S3 Standard → Standard-IA**.
- Does **not** support Glacier or One Zone-IA.
- Analyzes access patterns and generates a daily CSV report — useful for setting lifecycle policies.

---

### Storage Class Comparison

| Storage Class | Availability | Min Duration | Retrieval Fee | Use Case |
|---|---|---|---|---|
| Standard | 99.99% | None | None | Frequent access |
| Intelligent-Tiering | 99.9% | None | None | Unknown patterns |
| Standard-IA | 99.9% | 30 days | Yes | Infrequent, rapid retrieval |
| One Zone-IA | 99.5% | 30 days | Yes | Recreatable infrequent data |
| Express One Zone | 99.5% | 30 days | None | Ultra-low latency |
| Glacier Instant | 99.9% | 90 days | Yes | Archive with ms retrieval |
| Glacier Flexible | 99.99% | 90 days | Yes | Long-term archive |
| Glacier Deep Archive | 99.99% | 180 days | Yes | Cheapest, 7–10 yr retention |

![alt text](image.png)
![alt text](image-1.png)
![Storage class ordering](https://docs.aws.amazon.com/images/AmazonS3/latest/userguide/images/lifecycle-transitions-v4.png)

---

## Pricing

- **Storage Costs**: Charged per GB/month based on storage class.
- **Request Costs**: Charged per PUT, GET, LIST, etc. request. Glacier classes charge more per request.
- **Retrieval Costs**: IA and Glacier classes charge per GB retrieved.
- **Data Transfer In**: Free — no charge for uploading data into S3.
- **Data Transfer Out**: Charged based on GB transferred out to the internet or other regions. Free within the same region between AWS services.
- **S3 Requester Pays**: Bucket owner pays storage; requester pays data transfer. Requester must be an authenticated AWS account (cannot be anonymous). Useful for sharing large public datasets.
