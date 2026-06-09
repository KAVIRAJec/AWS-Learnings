## AWS Storage Gateway

AWS Storage Gateway is a hybrid cloud storage service that connects on-premises environments to AWS storage. It acts as a bridge — on-premises apps use standard protocols (NFS, SMB, iSCSI) while data is stored in AWS.

**Deployment options:**
- **VM Appliance**: Run Storage Gateway as a virtual machine on VMware, Hyper-V, or KVM — for environments with existing virtualization infrastructure.
- **AWS Hardware Appliance**: A physical 1U server with Storage Gateway software pre-installed — for environments that **do not use virtualization** (bare-metal / dedicated physical servers). Can be configured as File, Volume, or Tape gateway after activation.

### S3 File Gateway:
- Exposes an **NFS/SMB** interface to on-premises apps. Files are stored as objects in **S3**.
- Frequently accessed files are cached locally on the gateway for low-latency access.
- Supports S3 storage classes — Standard, IA, One Zone-IA, Intelligent-Tiering(Not Glacier).
- Use case: Replace on-premises file shares, back up files to S3, extend local storage to cloud.

### Volume Gateway:
- Provides **iSCSI block storage** volumes to on-premises servers. Backed by **EBS Snapshots** on S3 bucket in AWS.
- Two modes:
   - **Cached Mode**: Primary data stored in S3, frequently accessed data cached on-premises. Low local storage needed.
   - **Stored Mode**: Full dataset stored on-premises, asynchronously backed up to AWS S3 as EBS snapshots. Use for low-latency access to entire dataset with cloud backup.

### Tape Gateway:
- Virtual Tape Library (**VTL**) using **iSCSI** — integrates with existing backup software (Veeam, Veritas, etc.) without changes.
- Virtual tapes stored in **S3**, archived to **S3 Glacier** or **Glacier Deep Archive**.
- Use case: Replace physical tape infrastructure while keeping the same backup workflow.

| Gateway Type      | Protocol    | Backend          | Use Case                              |
|-------------------|-------------|------------------|---------------------------------------|
| S3 File Gateway   | NFS / SMB   | S3               | File storage & backup to S3           |
| Volume (Cached)   | iSCSI       | S3 + EBS Snapshots | Low-latency access to hot data      |
| Volume (Stored)   | iSCSI       | EBS Snapshots    | Full on-prem dataset + cloud backup   |
| Tape Gateway      | iSCSI (VTL) | S3 / Glacier     | Replace physical tape backup          |
