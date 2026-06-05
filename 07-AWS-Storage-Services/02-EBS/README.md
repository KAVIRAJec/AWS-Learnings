## AWS EBS (Elastic Block Store)

Amazon EBS is a block storage service designed for use with Amazon EC2 instances. It provides high-performance, durable, and scalable storage for workloads such as databases, file systems, and applications requiring low-latency access to data.
- EBS volumes behave like raw, unformatted block devices and can be mounted as file systems or used in applications (like a database). 
- One EC2 instance can have multiple EBS volumes attached to it but one EBS can be attached to only one EC2 instance at a time. Also, we can attach 1 EBS volume to multiple EC2 instances using **Multi-Attach** feature but only in the same availability zone.
- EBS are network attached(network drive), meaning they are not directly attached to the EC2 instance. Instead, they are connected over the network, allowing for flexibility and scalability.
- EC2 and EBS should be in the same region and availability zone.
- EBS volume attached to the root device of an EC2 instance is called the **root volume** and it by default has "delete on termination" enabled. This means that when the EC2 instance is terminated, the root volume is also deleted. However, you can change this setting to keep the root volume after termination if needed. Other than root volume, EBS volumes are not deleted when the EC2 instance is terminated by default. You can enable "delete on termination" manually for other EBS volumes if required.
![EBS](image-1.png)

### Key Features:
- **Durability and Availability:** EBS volumes are designed for 99.999% availability and automatically replicated within their Availability Zone to protect against hardware failures.
- **Volume Types:** EBS offers different volume types optimized for various workloads, including SSD-backed volumes for transactional workloads and HDD-backed volumes for throughput-intensive applications.
- **Encryption:** When you enable encryption, data is encrypted at rest and in transit, all data in flight is encrypted, and all snapshots are encrypted, all volumes created from the snapshot are encrypted. EBS encryption uses AWS Key Management Service (KMS) to manage encryption keys. Snapshots of non encrypted volumes are not encrypted, but you can create a snapshot of an unencrypted volume and then copy it to create an encrypted snapshot. You can also use the AWS CLI or SDKs to encrypt existing unencrypted volumes and snapshots.
- **Elastic Volumes (live modification):** EBS supports live configuration changes **while the volume is in use** — no need to detach, stop the instance, or cause any service interruption. You can modify:
  - **Volume type** (e.g., gp2 → gp3, or gp3 → io2)
  - **Volume size** (increase only — EBS size cannot be decreased)
  - **IOPS** (for gp3, io1, io2)
  - **Throughput** (for gp3)
  - After modification, the volume may enter an **optimizing** state while changes take effect in the background — I/O continues normally during this period.
- **Performance:** EBS volumes can deliver high IOPS (Input/Output Operations Per Second) and low latency, making them suitable for a wide range of applications, including databases and big data analytics.
- **Multi-Attach:** EBS volumes can be attached to multiple EC2 instances in the same Availability Zone, enabling high availability and failover scenarios. It is limited to 16 EC2 instances at a time & IO EBS volumes (io1 and io2) only support multi attach feature.
- **Boot Volumes:** Only GP2 and GP3 volumes, IO1 and IO2 volumes can be used as boot volumes.

### EBS Volume Types:
- **General Purpose SSD (gp2/gp3):** Balanced price and performance for a wide range of workloads. Suitable for boot volumes, small to medium-sized databases, and development/test environments. Max IOPS is 16,000 and max throughput is 1,000 MB/s, if require more IOPS increase the size of the volume.
   - **gp3:** Newer generation of gp2 volumes. Baseline: 3000 IOPS and 125 MB/s throughput. Max IOPS is 16,000 and max throughput is 1,000 MB/s.
   - **gp2:** Older generation. IOPS scales with volume size — **3 IOPS per GiB**, baseline between 100–16,000 IOPS. Uses a **burst credit (bucket) model**:
      - When usage is below the baseline, credits accumulate (max 5.4 million credits).
      - During I/O spikes, credits are consumed to burst up to **3,000 IOPS** regardless of volume size.
      - Once credits are exhausted, IOPS drops back to baseline (3 IOPS/GiB).
      - Throughput max: 250 MB/s.
      ```
      Low I/O   →  credits accumulate
      I/O spike →  credits consumed → burst to 3,000 IOPS
      Credits gone → IOPS drops to baseline
      ```
- **Provisioned IOPS SSD (io1/io2):** Designed for I/O-intensive applications — delivers provisioned IOPS **99.9% of the time**, no credit model, always-on. IOPS set independently of volume size. **Supports multi-attach**.
   - **io1:** Max 64,000 IOPS. Charged for provisioned IOPS 24/7 whether used or not.
   - **io2:** Max 256,000 IOPS, sub-millisecond latency, higher durability (99.999%).

   > **io1 vs gp2 cost decision**: If your io1 volume is **under-utilized with only occasional bursts**, switch to **gp2** — gp2's credit model handles spikes automatically at much lower cost. Use io1 only when you need **sustained, consistent high IOPS** continuously (e.g., production databases with constant heavy I/O).
- **Throughput Optimized HDD volumes (st1):** Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads. Suitable for big data, data warehouses, and log processing. Max throughtput 500 MiB/s and max IOPS is 500.
- **Cold HDD (sc1):** Lowest-cost HDD volume designed for less frequently accessed data. Suitable for large-scale data storage, such as infrequently accessed backups and archives. Max throughtput 250 MiB/s and max IOPS is 250.
- **Magnetic (standard):** Previous generation HDD volume type, now deprecated. Suitable for infrequently accessed data and workloads with lower performance requirements.

![alt text](image.png)

### EBS Snapshots:
- EBS allows you to create point-in-time snapshots of your volumes, which are stored in Amazon S3 and can copy across AZ/regions.
- Snapshots can be used for backup, disaster recovery, and creating new volumes. (Organizations use EBS snapshots to create AMIs and launch their software configured in the AMI). 
- To automatically create EBS snapshots, you can use **Amazon Data Lifecycle Manager (DLM)**.

**Amazon DLM (Data Lifecycle Manager):**
- Automates the creation, retention, copying, and deletion of **EBS snapshots and EBS-backed AMIs** on a schedule — no custom scripts or Lambda functions needed.
- Defined using **lifecycle policies** — specify what to back up, how often, how many copies to keep, and where to copy them.

**Key concepts:**
- **Target resources**: Tag-based — DLM identifies EBS volumes or instances by resource tags (e.g., `Environment=prod`).
- **Schedule**: Cron-based frequency — hourly, daily, weekly, or custom cron expression.
- **Retention**: Keep the last N snapshots (count-based) or snapshots within the last N days (age-based) — older ones are automatically deleted.
- **Cross-region copy**: Automatically copy snapshots to another region for disaster recovery.
- **Cross-account copy**: Share and copy snapshots to another AWS account.
- **Fast Snapshot Restore (FSR)**: Can be enabled on target snapshots within DLM policy — pre-warms snapshots for immediate full performance on restore.

**DLM policy types:**

| Policy Type | What it manages |
|---|---|
| **EBS snapshot policy** | Automates snapshots of individual EBS volumes |
| **EBS-backed AMI policy** | Automates AMI creation from EC2 instances (includes all attached volumes) |
| **Cross-account copy event policy** | Automates copying of snapshots shared from another account |

**DLM vs manual snapshots:**
- DLM handles the full lifecycle — create, copy, retain, and delete — automatically.
- Without DLM, you must write Lambda + EventBridge rules to replicate the same behavior.
- No additional cost for DLM itself — you pay only for the snapshot storage.
#### The below are under EBS snapshots(not S3):
- To save EBS snapshot costs(upto 75%), Setup EBS snapshot archive (takes 12 to 72 hours to restore), and use EBS snapshot lifecycle policies to automate the deletion of old snapshots.
- Can also enable EBS snapshot recycle bin to recover deleted snapshots. EBS snapshot recycle bin is a feature that allows you to recover deleted EBS snapshots within a specified retention period(1 day to 1 year). When you delete a snapshot, it is moved to the recycle bin instead of being permanently deleted. You can restore the snapshot from the recycle bin within the retention period.
- Fast snapshot recoveries (FSR) is a feature that allows you to create snapshots of EBS volumes without impacting the performance of the running instance. FSR uses incremental snapshots to minimize the impact on performance and reduce the time required to create a snapshot. It costs more than regular snapshots. 


### Pricing:
EBS pricing is based on 
- Volume type (SSD or HDD)
- Provisioned storage size (in GB)
- IOPS (for provisioned IOPS volumes)
- Data transfer (for snapshots and data transfer between regions)
- Snapshot storage (for EBS snapshots stored in S3)

#### Use Cases:
- **Database Storage:** EBS is commonly used for databases, providing low-latency access to data and high IOPS for transactional workloads.
- **File Systems:** EBS can be used to create file systems for applications that require persistent storage, such as web servers and application servers.
- **Backup and Disaster Recovery:** EBS snapshots can be used for backup and disaster recovery, allowing you to create point-in-time copies of your data.
- **Big Data Analytics:** EBS can be used for big data analytics workloads, providing high throughput and low latency for data processing.
