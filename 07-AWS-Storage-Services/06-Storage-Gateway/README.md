## AWS Storage Gateway

AWS Storage Gateway is a hybrid cloud storage service that enables on-premises applications to seamlessly use AWS cloud storage. It provides a bridge between on-premises environments and AWS cloud storage, allowing you to store data in the cloud while maintaining low-latency access to frequently accessed data.

### Key Concepts
- **Gateway**: A virtual appliance that connects on-premises environments to AWS storage services.
- **Volume Gateway**: Provides block storage volumes that can be mounted as iSCSI devices on-premises. It supports both cached and stored volumes.
- **File Gateway**: Provides a file interface(file based access) to AWS S3, allowing you to store and retrieve files using standard file protocols (NFS, SMB).
- **Tape Gateway**: Provides a virtual tape library (VTL) that can be used with existing backup applications. It allows you to store backup data in Amazon S3 and archive it to Amazon S3 Glacier.
- **Storage Gateway**: A virtual appliance that connects on-premises environments to AWS storage services. It can be deployed as a hardware appliance or as a virtual machine (VM) on VMware ESXi, Microsoft Hyper-V, or AWS EC2.

- **Cached Volume**: A volume that stores frequently accessed data in the cloud and retains a cache of the most recently used data on-premises for faster access.

### AWS Storage Gateway Types
| Gateway Type            | Protocol   | Cloud Backend   | Use Case                                   |
|--------------------------|------------|-----------------|--------------------------------------------|
| **File Gateway**         | NFS/SMB    | S3              | File storage to S3                         |
| **Volume Gateway - Cached** | iSCSI     | EBS Snapshots   | Low-latency access to most-used data       |
| **Volume Gateway - Stored** | iSCSI     | EBS Snapshots   | Full dataset on-prem, async backup to AWS  |
| **Tape Gateway**         | iSCSI (VTL)| S3/Glacier      | Replace on-prem tape infrastructure        |