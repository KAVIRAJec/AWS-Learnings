## EC2 Hibernate
- When you shut down your EC2 instance, the contents of the instance's memory (RAM) are lost, and the instance is stopped. You can start the instance again, but it will not retain any data in memory. When you create or start an instance, the boot volume is created from the AMI, and the instance starts with a clean state.
- When you stop your EC2 instance, the contents of the instance's memory (RAM) are saved to the EBS root volume, allowing you to resume your work later without losing any data.
- Hibernate is similar to stopping an instance, but it saves the contents of the instance's memory (RAM) to the EBS root volume instead of shutting down the instance completely.
- When you hibernate an instance, the instance's state is saved to the EBS root volume, and the instance is stopped. When you start the instance again, it resumes from the saved state from the EBS root volume, allowing you to continue working without losing any data.
- Hibernate is useful for workloads that require a quick startup time and want to retain the instance's state without losing any data.
- Condition for Hibernate:
  - The instance must be an EBS-backed instance.
  - The instance must have an EBS root volume that is configured for hibernation & must encrypted.
  - The instance must have sufficient EBS storage to save the contents of the instance's memory (RAM).
  - The instance must be stopped before it can be hibernated.
  - The RAM size must be less than 150 GB.
  - The instance must be in a supported instance type (e.g., T3, M5, C5, R5, etc.).
  - Available for On-demand, spot and reserved instances.
  - You cannot hibernate a system for more than 60 days.
   - **Hibernate is not available for all instance types and regions.**

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
- Instance Store is temporary storage that is physically attached to the host (EC2) server. Data is lost when the instance is stopped or terminated.
- It is suitable for temporary data or caching, and EBS volumes can be backed up using snapshots, while Instance Store data cannot be recovered after instance termination.
- Instance Store volumes are ephemeral and are not persistent across instance stops or terminations.
- The Instance store storage offers very high IOPS. They are ideal for applications that require high IOPS and low latency.
- Instance Store volumes are physically attached to the host server and are not network-attached like EBS volumes. This means that Instance Store volumes can provide higher performance for certain workloads, such as high-performance computing (HPC) or data-intensive applications.
