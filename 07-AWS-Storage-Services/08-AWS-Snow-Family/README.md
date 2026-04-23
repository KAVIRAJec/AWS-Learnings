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

### Snowball:
- A physical rugged device used to transfer large amounts of data (TBs to PBs) into/out of AWS when network transfer is too slow or costly. AWS ships the device to you → you load data → ship it back → AWS imports into S3.
- Encrypted with **256-bit KMS encryption** by default. Tamper-resistant and secure.
- Two variants:

#### Snowball Edge Storage Optimized:
- **Storage**: 80 TB usable (HDD) + 1 TB SSD.
- **Compute**: 40 vCPUs, 80 GiB RAM — enough to run EC2-compatible instances and Lambda functions locally.
- Best for bulk data migrations and local storage with basic compute needs.
- Snowball cannot directly load data to S3 Glacier. It could be Snowball -> S3 bucket -(lifecycle policy)-> Glacier.

#### Snowball Edge Compute Optimized:
- **Storage**: 42 TB usable HDD + 7.68 TB NVMe SSD.
- **Compute**: 52 vCPUs, 208 GiB RAM — optional **GPU** for ML inference, video analysis.
- Best for edge computing workloads where you need powerful processing in disconnected/remote environments (factories, ships, military).
- Supports EC2 instances (via Nitro), Lambda functions, and EKS Anywhere on-device.

**Common use cases:**
- Data center migration to AWS.
- Disaster recovery — backup large datasets offline.
- Edge computing in remote locations with no reliable internet (oil rigs, military sites).
