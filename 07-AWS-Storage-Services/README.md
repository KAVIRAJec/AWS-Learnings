## AWS Storage Services

AWS offers purpose-built storage for every workload — object, block, file, archival, and hybrid. Choosing the right service depends on access pattern, latency requirement, durability need, and cost.

| # | Service | Type | Best For |
|---|---|---|---|
| 1 | [Amazon S3](./01-S3/README.md) | Object | Unlimited files, backups, data lake, static hosting |
| 2 | [Amazon EBS](./02-EBS/README.md) | Block | EC2 instance boot volumes, databases, low-latency I/O |
| 3 | [Amazon EFS](./03-EFS/README.md) | File (NFS) | Shared file system across multiple EC2 / Lambda |
| 4 | [Amazon FSx](./04-FSx/README.md) | File (Windows/Lustre/ONTAP/OpenZFS) | Windows shares, HPC, NetApp ONTAP |
| 5 | [AWS Backup](./05-AWS-Backup/README.md) | Managed backup | Centralized backup across AWS services |
| 6 | [Storage Gateway](./06-Storage-Gateway/README.md) | Hybrid | On-premises to AWS bridge (file/volume/tape) |
| 7 | [AWS DataSync](./07-DataSync/README.md) | Data transfer | Migrate/sync on-premises storage to AWS |
| 8 | [AWS Snow Family](./08-AWS-Snow-Family/README.md) | Physical transfer | Move petabyte-scale data offline |
| 9 | [AWS Transfer Family](./09-AWS-Transfer-Family/README.md) | Managed file transfer | SFTP/FTPS/FTP directly into S3 or EFS |

---

## Storage Services — Protocol Reference

| Service | Protocol(s) | Access From | Notes |
|---|---|---|---|
| **Amazon S3** | HTTPS (REST API) | Any HTTP client, AWS SDK, CLI | Object storage — no mount, no file system. Mountpoint for S3 adds POSIX-style read mount via FUSE |
| **Amazon EBS** | Block device (NVMe / network block) | EC2 only (same AZ) | No network protocol visible to user — behaves like a local disk attached to one EC2 instance |
| **Amazon EFS** | **NFS** (NFSv4.1, NFSv4.2) | EC2, Lambda, ECS, on-prem (via DX/VPN) | Linux only — shared across multiple instances simultaneously |
| **FSx for Windows** | **SMB** (Server Message Block) | Windows EC2, on-prem via DX/VPN | Active Directory integration, Windows ACLs |
| **FSx for Lustre** | **Lustre** (parallel file system) | Linux EC2 | High-performance parallel I/O for HPC/ML; direct S3 integration |
| **FSx for NetApp ONTAP** | **NFS, SMB, iSCSI** | Linux + Windows + VMware | Multi-protocol NAS — all three simultaneously |
| **FSx for OpenZFS** | **NFS** (NFSv3, NFSv4) | Linux EC2 | ZFS-based, sub-millisecond latency |
| **Storage Gateway — File** | **NFS, SMB** | On-premises apps | Files stored as S3 objects behind the scenes |
| **Storage Gateway — Volume** | **iSCSI** | On-premises apps | Block volumes backed by S3 (cached or stored mode) |
| **Storage Gateway — Tape** | **iSCSI** (VTL) | On-premises backup software | Virtual tape library — replaces physical tape, data lands in S3 Glacier |
| **AWS DataSync** | NFS, SMB, HDFS, S3 API, EFS, FSx | On-premises → AWS | Agent-based; reads from source using native protocol |
| **AWS Snow Family** | **NFS** (Snowcone), **NFS + S3 API** (Snowball Edge) | On-premises | Physical devices — data loaded locally then shipped to AWS |
| **AWS Transfer Family** | **SFTP, FTPS, FTP, AS2** | Any SFTP/FTP client | Managed endpoints — files land in S3 or EFS |

---

## Service Overviews

### Amazon S3
Unlimited object storage. 11 nines (99.999999999%) durability. Objects stored as key–value pairs — no hierarchy, no file system. Scales to any size with zero management.
- **Storage classes** span hot → warm → cold → archive, trading access speed for cost.
- Cross-region replication, versioning, lifecycle policies, event notifications built-in.

### Amazon EBS
Persistent block storage attached to a single EC2 instance (one volume → one instance at a time, except Multi-Attach io1/io2). Network-attached but behaves like a local disk.
- Volume persists independently of instance lifecycle — detach, re-attach, snapshot.
- Lives in a single AZ — snapshots stored in S3 for cross-AZ/cross-region recovery.

### Amazon EFS
Fully managed NFS file system. **Multiple EC2 instances across multiple AZs** mount the same file system simultaneously — true shared storage.
- Elastic — grows and shrinks automatically, no provisioning.
- Lifecycle tiers (Standard → IA → Archive) reduce cost for infrequently accessed files.

### Amazon FSx
Managed file systems for specialized needs:
- **FSx for Windows File Server** — SMB protocol, Active Directory integration, Windows ACLs.
- **FSx for Lustre** — high-performance parallel file system for HPC, ML training, media rendering. Integrates directly with S3.
- **FSx for NetApp ONTAP** — enterprise NAS with multi-protocol (NFS, SMB, iSCSI).
- **FSx for OpenZFS** — ZFS-based, high IOPS, sub-millisecond latency.

### AWS Storage Gateway
Hybrid storage bridge — on-premises applications access AWS storage using standard protocols (NFS, SMB, iSCSI).
- **File Gateway** — files stored as S3 objects (NFS/SMB).
- **Volume Gateway** — iSCSI block volumes backed by S3 (cached) or local disks with S3 backup (stored).
- **Tape Gateway** — virtual tape library replacing physical tape, backed by S3 Glacier.

### AWS DataSync
Automated data transfer agent — accelerates moving data from on-premises (NFS, SMB, HDFS, object) or other clouds to AWS (S3, EFS, FSx). Up to 10× faster than open-source tools.

### AWS Snow Family
Physical devices shipped to your data center for offline bulk transfer or edge compute.
- **Snowcone** — 8 TB (smallest, portable).
- **Snowball Edge** — 80 TB, Storage Optimized or Compute Optimized.
- **Snowmobile** — exabyte-scale (truck).

### AWS Transfer Family
Fully managed SFTP, FTPS, FTP, and AS2 endpoints — clients transfer files directly into/out of S3 or EFS using familiar protocols without changing workflows.

---

## Pricing Comparison (US East — N. Virginia)

> Prices as of 2025–2026. Always verify at [aws.amazon.com/pricing](https://aws.amazon.com/pricing) for the latest.

### Amazon S3 — Storage Cost

| Storage Class | $/GB-month | Min Duration | Retrieval Fee | Use Case |
|---|---|---|---|---|
| **Standard** | $0.023 | None | None | Frequently accessed data |
| **Intelligent-Tiering** | $0.023 (active) | None | None | Unknown/changing access patterns |
| **Standard-IA** | $0.0125 | 30 days | $0.01/GB | Backups, disaster recovery |
| **One Zone-IA** | $0.010 | 30 days | $0.01/GB | Re-creatable infrequent data |
| **Glacier Instant Retrieval** | $0.004 | 90 days | $0.03/GB | Archives needing ms retrieval |
| **Glacier Flexible Retrieval** | $0.0036 | 90 days | $0.01/GB (Standard, 3–5 hr) | Long-term archives |
| **Glacier Deep Archive** | $0.00099 | 180 days | $0.02/GB (~12 hr) | Compliance, 7–10 yr retention |

> Intelligent-Tiering also charges $0.0025 per 1,000 objects/month as a monitoring fee.

**S3 Request Pricing (per 1,000 requests):**

| | PUT / POST / LIST | GET / SELECT |
|---|---|---|
| Standard / IT | $0.005 | $0.0004 |
| Standard-IA / One Zone-IA | $0.01 | $0.001 |
| Glacier classes | $0.05 | varies |

---

### Amazon EBS — Volume Cost

| Volume Type | $/GB-month | Extra Costs | Best For |
|---|---|---|---|
| **gp3** (General Purpose SSD) | $0.08 | +$0.005/IOPS above 3,000; +$0.04/MB/s above 125 MB/s | Default choice — boot, apps, DBs |
| **gp2** (General Purpose SSD) | $0.10 | IOPS scale with size (no separate charge) | Legacy — prefer gp3 |
| **io2** (Provisioned IOPS SSD) | $0.125 | +$0.065/IOPS-month (tiered above 32K/64K IOPS) | Critical DBs, sub-ms latency |
| **io1** (Provisioned IOPS SSD) | $0.125 | +$0.065/IOPS-month (flat) | Legacy io — prefer io2 |
| **st1** (Throughput HDD) | $0.045 | — | Big data, log processing, sequential |
| **sc1** (Cold HDD) | $0.015 | — | Infrequently accessed, lowest cost block |

> EBS Snapshots: **$0.05/GB-month** stored in S3 (regardless of volume type).

---

### Amazon EFS — File System Cost

| Storage Class | $/GB-month | Access Fee | Notes |
|---|---|---|---|
| **Standard** (Multi-AZ) | $0.30 | None | Highest durability, sub-ms latency |
| **Standard-IA** | $0.0165 | $0.01/GB read | Auto-tiered via lifecycle policy |
| **Archive** | $0.008 | $0.03/GB read | Data accessed a few times a year |
| **One Zone** | ~$0.16 | None | Single AZ, dev/test |
| **One Zone-IA** | ~$0.009 | $0.01/GB read | Single AZ infrequent |

> Elastic throughput (default): Reads $0.03/GB, Writes $0.06/GB transferred.

---

### Amazon FSx — File System Cost

| FSx Type | Deployment | Storage | $/GB-month |
|---|---|---|---|
| **FSx for Windows** | Single-AZ | SSD | $0.130 |
| **FSx for Windows** | Single-AZ | HDD | $0.013 |
| **FSx for Windows** | Multi-AZ | SSD | $0.230 |
| **FSx for Windows** | Multi-AZ | HDD | $0.025 |
| **FSx for Lustre** | Scratch (SSD) | SSD | $0.140 |
| **FSx for Lustre** | Persistent (SSD) | SSD | $0.145 |
| **FSx for Lustre** | Persistent (HDD) | HDD | ~$0.025 |

> FSx also charges separately for throughput capacity (MBps provisioned).

---

### Storage Gateway — Approximate Cost

| Gateway Type | Storage Charge | Data Written to AWS |
|---|---|---|
| **File Gateway** | Standard S3 rates apply | $0.01/GB (first 100 GB/account/month free) |
| **Volume Gateway** | $0.023/GB-month (AWS-side volume) | $0.01/GB |
| **Tape Gateway** | $0.023/GB-month (virtual tape in S3) | $0.01/GB |

> Tape archives moved to Glacier: $0.0036/GB-month; Deep Archive: $0.00099/GB-month.

---

## Cost Comparison at a Glance

| Service | Cheapest Option | $/GB-month | Most Expensive | $/GB-month |
|---|---|---|---|---|
| **S3** | Glacier Deep Archive | $0.00099 | Standard | $0.023 |
| **EBS** | sc1 (Cold HDD) | $0.015 | io1/io2 (+ IOPS cost) | $0.125+ |
| **EFS** | Archive | $0.008 | Standard (Multi-AZ) | $0.30 |
| **FSx Windows** | Single-AZ HDD | $0.013 | Multi-AZ SSD | $0.230 |
| **FSx Lustre** | Persistent HDD | ~$0.025 | Persistent SSD | $0.145 |

---

## When to Use What

| Scenario | Right Choice |
|---|---|
| Static website, images, videos, backups | S3 Standard |
| EC2 root/boot volume | EBS gp3 |
| High-IOPS production database on EC2 | EBS io2 |
| Shared file system across multiple EC2 instances | EFS Standard |
| Windows applications needing SMB/Active Directory | FSx for Windows |
| HPC, ML training with massive parallel I/O | FSx for Lustre |
| Long-term compliance archive (years) | S3 Glacier Deep Archive |
| On-premises apps writing to AWS storage | Storage Gateway |
| Migrate/sync NAS to AWS | DataSync |
| Transfer petabytes with no network | Snow Family |
| Legacy SFTP clients writing to S3 | Transfer Family |
