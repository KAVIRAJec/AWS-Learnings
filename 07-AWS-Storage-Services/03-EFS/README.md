## AWS EFS(Elastic File System)

- Amazon EFS is a fully managed, scalable, and elastic file storage service that provides shared file storage for use with AWS services and on-premises resources. It supports the NFS (Network File System) protocol, making it easy to integrate with applications requiring shared file systems.
- It is linux based and can be accessed concurrently by thousands of EC2 instances, and automatically grows/shrinks as you add/remove files.

### Key Concepts
- **File System**: A file system is a collection of files and directories that can be accessed by EC2 instances. It is a storage container in EFS (like a bucket in S3)
- **Mount Target**: A mount target is an endpoint in your VPC that allows EC2 instances to access the file system. Each mount target is associated with a specific availability zone and subnet in your VPC.
- **Protocol**: EFS uses the NFS (Network File System) protocol, which allows multiple EC2 instances to access the file system concurrently. (NFSv4.1 is the default version, but NFSv4.2 is also supported)
- **OS Support**: EFS is compatible with Linux distributions, allowing for flexible deployment options.

### Performance Modes
- **General Purpose**: Designed for latency-sensitive use cases, such as web serving environments, content management systems, and home directories. It provides low-latency access to files and is suitable for a wide range of applications.
- **Max I/O**: Designed for high-throughput and high-parallelism workloads, such as big data analytics, media processing, and machine learning. It provides higher throughput and IOPS but may have slightly higher latencies compared to the General Purpose mode.

### Storage Classes
- **Standard**: The default storage class for EFS, designed for frequently accessed files. It provides low-latency access and high throughput, making it suitable for a wide range of applications.(~$0.30/GB/month)
- **Infrequent Access (IA)**: Designed for files that are not accessed frequently but still need to be retained. It provides lower storage costs compared to the Standard class but has higher retrieval costs. It is suitable for use cases such as backups, archives, and infrequently accessed data.( ~$0.025/GB/month).

### Use Cases
- **Web Serving**: EFS can be used to store and serve web content, allowing multiple EC2 instances to access the same files concurrently. This is useful for web applications that require shared storage for images, videos, and other assets.
- **Content Management**: EFS can be used as a shared file system for content management systems, allowing multiple users to access and collaborate on files stored in EFS.

### Interview Questions
1. **Difference between EFS and EBS**
    - EFS is a fully managed, scalable, and elastic file storage service that provides shared file storage for use with AWS services and on-premises resources. It supports the NFS (Network File System) protocol, making it easy to integrate with applications requiring shared file systems.
    - EBS is a block-level storage service designed for use with Amazon EC2 instances. It provides persistent block storage that can be attached to EC2 instances, allowing them to store data and run applications.

2. **How does EFS integrate with on-premises resources?**
    - EFS can be accessed from on-premises resources using AWS Direct Connect or AWS VPN. This allows organizations to extend their on-premises applications to the cloud and use EFS as a shared file system for their applications.

3. **Can you access EFS from multiple Availability Zones?**
    - Yes, EFS is designed to be accessed concurrently from multiple Availability Zones, providing high availability and durability for your data.

4. **What is EFS lifecycle management?**
    - EFS lifecycle management is a feature that automatically moves files between the Standard and Infrequent Access storage classes based on access patterns (haven't accessed 7–90 days). This helps optimize storage costs by moving infrequently accessed files to the lower-cost Infrequent Access class while keeping frequently accessed files in the Standard class.