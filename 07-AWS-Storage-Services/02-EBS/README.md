## AWS EBS (Elastic Block Store)

Amazon EBS is a block storage service designed for use with Amazon EC2 instances. It provides high-performance, durable, and scalable storage for workloads such as databases, file systems, and applications requiring low-latency access to data.
- EBS volumes behave like raw, unformatted block devices and can be mounted as file systems or used in applications (like a database). 
- One EC2 instance can have multiple EBS volumes attached to it but one EBS can be attached to only one EC2 instance at a time. Also, we can attach 1 EBS volume to multiple EC2 instances using **Multi-Attach** feature.
- EC2 and EBS should be in the same region and availability zone.
![EBS](image-1.png)

### Key Features:
- **Durability and Availability:** EBS volumes are designed for 99.999% availability and automatically replicated within their Availability Zone to protect against hardware failures.
- **Volume Types:** EBS offers different volume types optimized for various workloads, including SSD-backed volumes for transactional workloads and HDD-backed volumes for throughput-intensive applications.
- **Snapshots:** EBS allows you to create point-in-time snapshots of your volumes, which are stored in Amazon S3. Snapshots can be used for backup, disaster recovery, and creating new volumes.(Organization use EBS snapshots to create AMIs and Launch their Softwares configured in the AMI). To automatically create EBS snapshots, you can use **AWS Data Lifecycle Manager**. Snapshots are stored in S3 and available in regional level rather then availability zone level like EB.
- **Encryption:** EBS supports encryption at rest and in transit, providing an additional layer of security for sensitive data.
- **Elasticity:** EBS volumes can be resized and modified without downtime, allowing you to adapt to changing storage needs.
- **Performance:** EBS volumes can deliver high IOPS (Input/Output Operations Per Second) and low latency, making them suitable for a wide range of applications, including databases and big data analytics.
- **Multi-Attach:** EBS volumes can be attached to multiple EC2 instances in the same Availability Zone, enabling high availability and failover scenarios.

### EBS Volume Types:
- **General Purpose SSD (gp2/gp3):** Balanced price and performance for a wide range of workloads. Suitable for boot volumes, small to medium-sized databases, and development/test environments. Max IOPS is 16,000 and max throughput is 1,000 MB/s, if require more IOPS increase the size of the volume.
- **Provisioned IOPS SSD (io1/io2):** Designed for I/O-intensive applications, such as large relational or NoSQL databases. Provides high IOPS and low latency. Max IOPS is 64,000 and max throughput is 1,000 MB/s. No need to increase the size of the volume to increase IOPS.
- **Throughput Optimized HDD (st1):** Low-cost HDD volume designed for frequently accessed, throughput-intensive workloads. Suitable for big data, data warehouses, and log processing.
- **Cold HDD (sc1):** Lowest-cost HDD volume designed for less frequently accessed data. Suitable for large-scale data storage, such as infrequently accessed backups and archives.
- **Magnetic (standard):** Previous generation HDD volume type, now deprecated. Suitable for infrequently accessed data and workloads with lower performance requirements.

![alt text](image.png)

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

### Interview Questions:
1. **Difference between gp2 and gp3 EBS volumes?**
   - gp2 volumes are designed for general-purpose workloads, while gp3 volumes offer better performance and cost savings. gp3 allows you to provision IOPS and throughput independently of storage size, making it more flexible and cost-effective.

2. **Difference between io1 and io2 EBS volumes?**
   - io1 volumes are designed for I/O-intensive applications, while io2 volumes provide higher durability and performance. io2 volumes can deliver up to 64,000 IOPS and 1,000 MB/s throughput, making them suitable for mission-critical applications.

3. **Can you create EC2 of storage type other than gp or io?**
   - No, you can only create EC2 instances with gp or io storage types. Other storage types (st1, sc1) are not supported for EC2 instances.