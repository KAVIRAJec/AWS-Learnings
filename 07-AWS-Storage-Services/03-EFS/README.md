## AWS EFS(Elastic File System)

- Amazon EFS is a fully managed, scalable, and elastic network file storage service that provides shared file storage for use with AWS services and on-premises resources. It supports the NFS (Network File System) protocol, making it easy to integrate with applications requiring shared file systems.
- It is linux based and can be accessed concurrently by thousands of EC2 instances, and automatically grows/shrinks as you add/remove files.
- Used security groups to control access to EFS. EFS is designed for use with Amazon EC2 instances and can be accessed from on-premises resources using AWS Direct Connect or AWS VPN.
- EFS is higher price than the EBS.

### Key Concepts
- **File System**: A file system is a collection of files and directories that can be accessed by EC2 instances. It scales automatically.
- **Mount Target**: A mount target is an endpoint in your VPC that allows EC2 instances to access the file system. Each mount target is associated with a specific availability zone and subnet in your VPC.
- **Protocol**: EFS uses the NFS (Network File System) protocol, which allows multiple EC2 instances to access the file system concurrently. (NFSv4.1 is the default version, but NFSv4.2 is also supported)
- **OS Support**: EFS is compatible with Linux distributions, allowing for flexible deployment options.
- **Multi AZ**: EFS is accessible from multiple availability zones, providing high availability and durability for your data. It automatically replicates data across multiple availability zones within a region.
- **Mount Targets**: EFS uses mount targets to provide access to the file system from EC2 instances. Each mount target is associated with a specific availability zone and subnet in your VPC. You can create multiple mount targets in different availability zones for high availability and redundancy.

### Performance Modes
- **General Purpose**: Designed for latency-sensitive use cases, such as web serving environments, content management systems, and home directories. It provides low-latency access to files and is suitable for a wide range of applications.
- **Max I/O**: Designed for high-throughput and high-parallelism workloads, such as big data analytics, media processing, and machine learning. It provides higher throughput and IOPS but may have slightly higher latencies compared to the General Purpose mode.
- **Throughput Mode**: EFS allows you to provision throughput independently of the amount of data stored in the file system.
    - **Bursting Throughput**: The default mode, which allows you to burst to higher throughput levels based on usage patterns.
    - **Provisioned Throughput**: Allows you to provision a specific throughput level for your file system, regardless of the Storage. This is useful for applications with consistent and predictable workloads that require high throughput.
    - **Elastic Throughput**: Automatically adjusts the throughput of your file system based on the amount of data stored. It is suitable for applications with variable workloads and unpredictable access patterns.
    
### EFS Lifecycle Management
- EFS lifecycle management is a feature that automatically moves files between the Standard and Infrequent Access storage classes based on access patterns. This is done by describing the access patterns of files and moving them to the appropriate storage class while creation.

### Storage Classes
- **Standard**: The default storage class for EFS, designed for frequently accessed files. It provides low-latency access and high throughput, making it suitable for a wide range of applications.(~$0.30/GB/month)
- **Infrequent Access (IA)**: Designed for files that are not accessed frequently but still need to be retained. It provides lower storage costs compared to the Standard class but has higher retrieval costs. It is suitable for use cases such as backups, archives, and infrequently accessed data.( ~$0.025/GB/month).
- **EFS Archive**: A lower-cost storage class for long-term storage of infrequently accessed files. It is designed for data that is rarely accessed but needs to be retained for compliance or regulatory purposes.
- **EFS One Zone**: A lower-cost storage class for data that is stored in a single availability zone. It is suitable for use cases where data redundancy across multiple availability zones is not required. It provides lower storage costs compared to the Standard class but has higher retrieval costs.

### Use Cases
- **Web Serving**: EFS can be used to store and serve web content, allowing multiple EC2 instances to access the same files concurrently. This is useful for web applications that require shared storage for images, videos, and other assets.
- **Content Management**: EFS can be used as a shared file system for content management systems, allowing multiple users to access and collaborate on files stored in EFS.

### Interview Questions
1. **Difference between EFS and EBS**
    | Feature                | EFS                                                                                                   | EBS                                                                                     |
    |------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
    | **Type**              | Network Attached Storage (NAS)                                                                       | Block-level storage                                                                     |
    | **Use Case**          | Shared file storage for multiple EC2 instances                                                       | Persistent block storage for a single EC2 instance                                     |
    | **Access**            | Can be accessed concurrently by multiple EC2 instances across multiple AZ, region, VPC                                               | Attached to a single EC2 instance at a time within AZ                                          |
    | **OS Installation**   | Not supported(Network server)                                                                           | Supported(via AMI)                                                                          |
    | **Scalability**       | Automatically scales as files are added or removed                                                   | Fixed size; requires manual resizing                                                  |
    | **Protocol**          | Supports linux based NFS (Network File System)                                                                   | Not protocol-based; operates at the block level                                       |
    | **Analogy**           | Like a Virtual Hard Disk (VHD) that can be shared across multiple EC2 instances                      | Like a physical hard drive attached to a single EC2 instance                          |
    | **Integration**       | Designed for use with multiple EC2 instances and on-premises resources                               | Designed for use with a single EC2 instance                                           |
    | **Primary Use**       | Shared file systems, web serving, content management, and concurrent access                          | Running applications, databases, and storing data for a single EC2 instance           |
    | **Availability** | EC2 and EFS can be in different VPC & AZs                               | EC2 and EBS should be in the same AZs                                   |

2. **How does EFS integrate with on-premises resources?**
    - EFS can be accessed from on-premises resources using AWS Direct Connect or AWS VPN.This allows organizations to extend their on-premises applications to the cloud and use EFS as a shared file system for their applications.

3. **Can you access EFS from multiple Availability Zones?**
    - Yes, EFS is designed to be accessed concurrently from multiple Availability Zones, providing high availability and durability for your data.

4. **What is EFS lifecycle management?**
    - EFS lifecycle management is a feature that automatically moves files between the Standard and Infrequent Access storage classes based on access patterns (haven't accessed 7–90 days). This helps optimize storage costs by moving infrequently accessed files to the lower-cost Infrequent Access class while keeping frequently accessed files in the Standard class.