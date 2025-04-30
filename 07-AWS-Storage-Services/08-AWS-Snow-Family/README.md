## AWS Snow Family
The AWS Snow Family consists of physical devices designed to help customers move large amounts of data into and out of AWS. These devices are ideal for environments with limited or no network connectivity or for transferring petabytes of data efficiently. The Snow Family includes AWS Snowcone, AWS Snowball, and AWS Snowmobile.

### Key Concepts
| Device                          | Storage Capacity | Compute                | Key Use Case                                      |
|---------------------------------|------------------|------------------------|--------------------------------------------------|
| Snowcone                        | 8 TB (SSD)       | 2 vCPUs               | IoT, edge computing, lightweight data transfer  |
| Snowball Edge Storage Optimized | 80 TB usable     | Optional              | Bulk data transfer with some compute            |
| Snowball Edge Compute Optimized | 42 TB usable     | 52 vCPUs, GPU optional| Edge compute with machine learning or video analytics |
| Snowmobile                      | Up to 100 PB     | No compute            | Datacenter migration (exabyte-scale)            |

- **OpsHub**: A web-based interface for managing Snow Family devices, allowing users to configure, monitor, and manage data transfer jobs.
- **OpsWorks**: AWS Snow Family devices can be managed using the AWS OpsWorks service, which provides a way to automate and manage the deployment of applications on these devices.

### Interview Questions
1. **Difference between SnowFamily and DataSync?**
   - **AWS Snow Family** is a physical device for transferring large amounts of data, while **AWS DataSync** is a service for automating data transfer between on-premises storage and AWS.

2. **What is AWS Snowmobile, and when would you use it?**
Snowmobile is a 45-foot shipping container designed for exabyte-scale data transfers, typically used for data center migrations.