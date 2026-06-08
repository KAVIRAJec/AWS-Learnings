## EC2 Hibernate

**Normal stop vs Hibernate:**

| | Normal Stop | Hibernate |
|---|---|---|
| **RAM contents** | Lost — cleared on stop | Saved to EBS root volume |
| **On restart** | Cold start — OS boots fresh, apps reload | Resumes exactly where it left off — RAM restored |
| **Startup time** | Full boot + app initialization | Fast — memory state already loaded |
| **Use case** | Regular stop/start | Workloads needing fast resume without losing in-memory state |

**How it works:**
- On hibernate: RAM contents are written to the **encrypted EBS root volume**, then the instance stops.
- On start: RAM is restored from EBS, the OS and applications resume from the saved state — no re-initialization needed.
- Saves time by eliminating the application warm-up period on restart.

**Critical constraint — must be enabled at launch:**
- **Hibernation cannot be enabled or disabled after an instance is launched.** It must be configured at launch time.
- If an existing instance does not have hibernation enabled, you must **launch a new instance** with hibernation enabled and migrate the workload — you cannot modify the existing instance.

**Requirements:**
- EBS-backed instance only (not instance store-backed).
- EBS root volume must be **encrypted** and large enough to hold the RAM contents.
- RAM size must be **less than 150 GB**.
- Supported instance families: T3, M5, C5, R5, and others — not all types supported.
- Available for On-Demand, Reserved, and Spot instances.
- **Maximum hibernate duration: 60 days** — instance cannot remain hibernated longer than 60 days.

**Billing during hibernation:**
- You pay only for the **EBS volumes** and **Elastic IP addresses** attached to the instance.
- No hourly EC2 instance charge — same as a stopped instance.

---

## EC2 AMI (Amazon Machine Image)
- An AMI is a pre-configured template that contains the operating system, application server, and software applications required to launch an EC2 instance. It serves as a blueprint for creating new instances and can be customized to meet specific requirements.
- AMIs can be created from existing EC2 instances or can be obtained from the AWS Marketplace. They can be shared with other AWS accounts or made public for others to use.
- AMIs are region-specific, meaning they can only be used in the region where they were created. However, you can copy AMIs to other regions if needed.

**What happens under the hood when you copy an AMI (Region A → Region B):**

An AMI is not a single file — it is a **metadata record** that points to one or more **EBS snapshots** (the actual data). Copying an AMI means copying those snapshots across regions.

```
Region A                              Region B
────────                              ────────
AMI (ami-aaaa)                        AMI (ami-bbbb)  ← new AMI ID created
  └── EBS Snapshot (snap-1111)   ──▶  └── EBS Snapshot (snap-2222)  ← full copy of snap-1111
        (stored in S3 internally)             (now lives in Region B's S3)
```

**Step-by-step background process:**
1. AWS reads the source AMI's metadata (block device mappings, permissions, virtualization type).
2. For each EBS snapshot attached to the AMI, AWS **copies the snapshot data** from Region A's S3 to Region B's S3 — this is the actual data transfer.
3. New snapshot IDs are created in Region B (`snap-2222`) — completely independent of the source.
4. A new AMI (`ami-bbbb`) is registered in Region B pointing to those new snapshots.
5. The source AMI and snapshots in Region A are **unchanged** — copy is non-destructive.

**Key points:**
- The copy produces **new, independent IDs** (AMI ID + Snapshot ID) in the destination region.
- Encryption can be changed during copy — you can re-encrypt with a different KMS key in Region B.
- Data transfer charges apply for moving snapshot data across regions.
- Instance Store-backed AMIs (not EBS) copy the entire bundle from S3 to the destination region's S3.
- AMIs can be categorized into three types:
   - **Public AMIs**: Available to all AWS users and can be used to launch instances without any restrictions. These AMIs are created by AWS.
   - **Private AMIs**: Created by users and are only accessible to the AWS account that created them. Private AMIs can be shared with specific AWS accounts or made public if desired.
   - **Marketplace AMIs**: Available for purchase from the AWS Marketplace. These AMIs are created by third-party vendors and sold in AWS Marketplace and can include pre-installed software and applications.

**Sharing an Encrypted AMI to Another AWS Account:**

An encrypted AMI's snapshots are encrypted with a KMS key. To share it cross-account, both the AMI **and** the KMS key must be shared — the target account needs the key to decrypt the snapshot when copying or launching.

- **Constraint**: AMIs encrypted with **AWS Managed Keys** (`aws/ebs`) **cannot be shared** — AWS Managed Keys are account-scoped and cannot be granted to external accounts. Must use a **Customer Managed Key (CMK)**.

```
─── SOURCE ACCOUNT ───────────────────────────────────────────────────

  1. Encrypt the AMI snapshot with a Customer Managed Key (CMK)
     (not aws/ebs — that cannot be shared cross-account)

  2. Share the AMI with the target account:
     EC2 Console → AMI → Modify Image Permissions → Add target Account ID

  3. Update the CMK key policy to grant the target account:
     - kms:DescribeKey
     - kms:ReEncrypt*
     - kms:CreateGrant
     - kms:Decrypt

─── TARGET ACCOUNT ───────────────────────────────────────────────────

  4. Copy the shared AMI into the target account
     (specify a CMK in the target account to re-encrypt the snapshot)
     → This produces a fully owned AMI in the target account

  5. Launch EC2 instances from the copied AMI
```

- **Why copy instead of launching directly from the shared AMI?** Copying re-encrypts the snapshot with the target account's own CMK — the target account becomes the full owner and no longer depends on the source account's key or permissions.
- If the source account revokes the CMK share before the target copies the AMI, the target loses access permanently.
- The copy step is **recommended** — direct launch from a shared encrypted AMI is possible but leaves a dependency on the source account's key.

---

## Golden AMI & User Data Scripts
- **Golden AMI**: A pre-configured AMI that contains the operating system, application server, and software applications are pre-installed required to launch an EC2 instance. It is used as a standard template for creating new instances and can be customized to meet specific requirements. It is faster to launch instances from a Golden AMI than from a standard AMI.
- **User Data Scripts**: Scripts that run when an instance is launched. They are used to perform common automated configuration tasks and run only once at launch time. User data scripts can be used to install software, configure settings, and perform other tasks during the instance launch process. They can be written in shell script or cloud-init format and can be passed as a parameter when launching an instance. It is slower than launching from a Golden AMI.

---

## Instance Store
- Instance Store is temporary storage that is **physically attached to the host server** — not network-attached like EBS. Provides very high IOPS and low latency.
- **Data is permanently lost** when the instance is stopped or terminated — instance store is ephemeral, not persistent.
- Cannot be backed up using snapshots — unlike EBS volumes.
- Ideal for temporary data, caching, buffers, scratch space, or high-IOPS workloads (HPC, data-intensive apps).

**Instance store-backed vs EBS-backed instances:**

| | EBS-backed | Instance Store-backed |
|---|---|---|
| **Root volume** | EBS volume | Instance store volume |
| **Can be stopped** | Yes — and restarted | **No — can only be rebooted or terminated** |
| **Data on stop** | EBS data preserved | N/A (cannot stop) |
| **Data on termination** | EBS deleted (if delete-on-termination set) | All instance store data lost |

> An **EBS-backed instance can also have additional instance store volumes attached**. If that instance is stopped and started, the EBS root volume is preserved but all data on the attached instance store volumes is lost.

**What happens when an EBS-backed instance is stopped and started:**

| | Behavior |
|---|---|
| **Underlying host** | AWS **may move the instance to a new physical host** — not guaranteed to be the same hardware |
| **Instance store data** | **Lost** — physically tied to the original host |
| **EBS volumes** | Preserved — network-attached, not tied to the host |
| **ENI (Elastic Network Interface)** | **Stays attached** — not detached on stop |
| **Elastic IP (EIP)** | **Remains associated** — EIP stays with the instance when stopped (EC2-VPC). Only disassociated in the legacy EC2-Classic platform (retired) |
| **Public IP (non-EIP)** | Lost — a new public IP is assigned on restart |
| **Private IP** | Preserved |
