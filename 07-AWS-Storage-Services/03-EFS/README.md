## AWS EFS(Elastic File System)

- Amazon EFS is a fully managed, scalable and elastic network file storage service that provides shared file storage for use with AWS services and on-premises resources. It supports the NFS (Network File System) protocol, making it easy to integrate with applications requiring shared file systems.
- It is linux based and can be accessed concurrently by thousands of EC2 instances, and automatically grows/shrinks as you add/remove files.
- Used security groups to control access to EFS. EFS is designed for use with Amazon EC2 instances and can be accessed from on-premises resources using AWS Direct Connect or AWS VPN.
- EFS is higher price than the EBS.(3x gp2)

### Key Concepts
- **File System**: A file system(POSIX) is a collection of files and directories that can be accessed by EC2 instances. It scales automatically(Serverless)
- **Mount Target**: An **ENI (Elastic Network Interface)** created by EFS inside a specific subnet in your VPC — this is the endpoint EC2 instances connect to. Mount targets are **managed by EFS**, not hosted on EC2 instances.
  - Create **one mount target per AZ** where EC2 instances need access.
  - Always mount from the **same AZ as the mount target** — traffic stays within the AZ, giving lowest latency and avoiding cross-AZ data transfer costs.
  - Cross-AZ access (EC2 in AZ-A connecting to mount target in AZ-B) works but adds latency and incurs data transfer charges.
  ```
  AZ-A: EC2 → Mount Target A (ENI)  ← same AZ, lowest latency, no transfer cost
  AZ-B: EC2 → Mount Target B (ENI)  ← same AZ, lowest latency, no transfer cost
  AZ-C: EC2 → Mount Target C (ENI)  ← same AZ, lowest latency, no transfer cost
  ```
  > **Best practice**: Create a mount target in every AZ where your EC2 instances run — each instance mounts via its local AZ mount target.
- **Protocol**: EFS uses the NFS (Network File System) protocol, which allows multiple EC2 instances(uptp 1000 clients & 10Gbs+ throughput) to access the file system concurrently. (NFSv4.1 is the default version, but NFSv4.2 is also supported)
- **OS Support**: EFS is compatible with Linux distributions, allowing for flexible deployment options.
- **Multi AZ**: EFS is accessible from multiple availability zones, providing high availability and durability for your data. It automatically replicates data across multiple availability zones within a region.
- **Mount Targets**: EFS uses mount targets to provide access to the file system from EC2 instances. Each mount target is associated with a specific availability zone and subnet in your VPC. You can create multiple mount targets in different availability zones for high availability and redundancy.

### Performance Modes
- **General Purpose** (default): Low-latency access — for web serving, CMS, home directories, genomics. Recommended for most workloads.
- **Max I/O**: Higher throughput and parallelism but slightly higher latency — for big data, media processing, ML with thousands of concurrent clients.

---

### Throughput Modes

**Performance mode and throughput mode are independent settings** — you pick one of each.

#### Bursting Throughput (default)
- Works like a **credit system** — EFS accumulates burst credits when the file system is idle or lightly used, and spends them during spikes.
- Base rate: **50 MiB/s per TiB** of storage.
- Burst rate: up to **100 MiB/s** (for file systems < 1 TiB) or higher for larger file systems.
- File systems under 1 TiB can burst at 100 MiB/s regardless of size — credits build up during idle time.
- **Best for**: Sporadic, unpredictable workloads with low average throughput but occasional spikes (e.g., genomics, batch processing). No manual configuration needed.

```
Idle / low usage  →  credits accumulate
Spike in I/O      →  credits consumed → burst to high throughput
Idle again        →  credits refill
```

#### Provisioned Throughput
- You specify a fixed MiB/s value — EFS delivers that throughput regardless of file system size or usage.
- Charged for provisioned throughput even when not used.
- **Best for**: Consistent, predictable high-throughput workloads where burst credits would deplete too fast.

#### Elastic Throughput
- Automatically scales throughput up and down with actual workload — no credits, no manual provisioning.
- Pay only for what you use (per GiB read/written).
- **Best for**: Variable workloads where you can't predict usage patterns and don't want to manage credits.

---

### Throughput Mode Comparison

| | Bursting | Provisioned | Elastic |
|---|---|---|---|
| **How throughput is set** | Credit-based, scales with file system size | You set a fixed MiB/s value | Auto-scales with workload |
| **Cost model** | Included in storage price | Extra charge for provisioned MiB/s | Pay per GiB transferred |
| **Best for** | Sporadic spikes, low average usage | Consistent high throughput needs | Unpredictable variable workloads |
| **Manual config** | No | Yes | No |

> **Important**: Storage class (Standard / IA / One Zone) and throughput mode are **completely independent** — switching to One Zone does NOT change your throughput mode, and enabling IA does NOT affect throughput. These are separate configurations.
    
### EFS Lifecycle Management
- EFS lifecycle management is a feature that automatically moves files between the Standard and Infrequent Access storage classes based on access patterns. This is done by describing the access patterns of files and moving them to the appropriate storage class while creation.

### Storage Classes
- **Standard**: The default storage class for EFS, designed for frequently accessed files. It provides low-latency access and high throughput, making it suitable for a wide range of applications.(~$0.30/GB/month)
- **Infrequent Access (IA)**: Lower storage cost (~$0.025/GB/month) but **charges a retrieval fee per GiB read** and adds latency on access. Files moved to IA via lifecycle management are automatically moved back to Standard when accessed — but relying on IA during burst workloads causes retrieval latency and extra cost. Not suitable as a throughput optimization strategy.
- **EFS Archive**: A lower-cost storage class for long-term storage of infrequently accessed files. It is designed for data that is rarely accessed but needs to be retained for compliance or regulatory purposes.
- **EFS One Zone**: A lower-cost storage class for data that is stored in a single availability zone. It is suitable for use cases where data redundancy across multiple availability zones is not required. It provides lower storage costs compared to the Standard class but has higher retrieval costs.

### Use Cases
- **Web Serving**: EFS can be used to store and serve web content, allowing multiple EC2 instances to access the same files concurrently. This is useful for web applications that require shared storage for images, videos, and other assets.
- **Content Management**: EFS can be used as a shared file system for content management systems, allowing multiple users to access and collaborate on files stored in EFS.
