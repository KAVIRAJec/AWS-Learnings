## AWS FSx

Amazon FSx is a fully managed file storage service that provides high-performance file systems for specific workloads. You pick the file system type based on your OS, protocol, and performance needs.

### FSx for Windows File Server:
- Fully managed Windows-native shared file storage built on **Windows Server**.
- Uses **SMB (Server Message Block)** protocol — compatible with Windows apps, .NET, SQL Server, SharePoint.
- Integrates with **Microsoft Active Directory** (on-premises or AWS Managed AD) for user authentication.
- Supports **DFS (Distributed File System)** namespaces for organizing shares across multiple servers.
- Can be accessed from Linux clients too (via SMB).
- Scale up to 10 GB/s, millions of IOPS, 100s PB of data
- Storage Option: SSD or HDD
- Can be accessable from on-premises via AWS Direct Connect or VPN, enabling hybrid workloads.
- Can be configured Multi-AZ for high availability
- Data daily backup to S3

### FSx for Lustre:
- High-performance parallel file system for compute-intensive workloads — **HPC(High Performance Computing), ML training, big data analytics, video processing**.
- Scales upto 100 GB/s hundreds, millions of IOPS, and sub-millisecond latency.
- Storage: SSD and HDD
- Can be **linked to an S3 bucket** — Can read and write data directly to S3(through FSx)
- Can be accessed from on-premises via AWS Direct Connect or VPN, enabling hybrid workloads.
- Two deployment types:
   - **Scratch**: Temporary, high-performance storage. No replication — data lost if file server fails. Use for short-term jobs.
   - **Persistent**: Replicated within the same AZ. Use for long-running workloads needing durability. Replace failed files within minutes.

### FSx for NetApp ONTAP:
- Managed ONTAP file system supporting **NFS, SMB, and iSCSI** — multi-protocol access from Linux, Windows, and macOS.
- Supports advanced features: **snapshots, replication, low cost compression, deduplication, tiering to S3**.
- Multi-AZ deployment for high availability and durability.
- Compatible with on-premises NetApp environments, windows, mac, Linux, ECS, VMware, EKS, and more.
- Point in time instantaneous cloning for dev/test environments.

### FSx for OpenZFS:
- Managed file system based on **OpenZFS**, accessible via **NFS**.
- Supports snapshots, compression, and point-in-time cloning(no data de-duplication).
- Upto to 1,000,000 IOPS and <0.5ms latency.
- Best for Linux/Unix workloads migrating from on-premises ZFS storage.

| FSx Type               | Protocol        | Best For                              |
|------------------------|-----------------|---------------------------------------|
| Windows File Server    | SMB             | Windows apps, Active Directory        |
| Lustre                 | Lustre (POSIX)  | HPC, ML, analytics                    |
| NetApp ONTAP           | NFS, SMB, iSCSI | Enterprise apps, multi-protocol       |
| OpenZFS                | NFS             | Linux/Unix apps, ZFS migration        |

