# AWS Global Infrastructure Concepts

The AWS Global Infrastructure is a combination of data centers, networks, and technologies distributed globally to deliver a fast, flexible, and secure cloud experience.

## AWS Service Scopes

### Global (not tied to any region)
| Service | Notes |
|---|---|
| **IAM** | Users, groups, roles, policies — global; STS has a global endpoint but also regional endpoints |
| **Route 53** | Global DNS service |
| **CloudFront** | CDN — edge locations worldwide; ACM certificates for CloudFront must be in `us-east-1` |
| **WAF** (with CloudFront) | Global when attached to CloudFront; regional when attached to ALB/API GW |
| **Global Accelerator** | Anycast IPs are global; endpoint groups are regional |
| **AWS Organizations** | Account management — global |
| **AWS Billing / Cost Explorer** | Global across all regions |
| **Direct Connect Gateway** | Global resource — connects one DX connection to VPCs in multiple regions |
| **S3 bucket names** | Globally unique — but bucket data lives in the region you choose |

---

### Regional (scoped to one AWS Region)
| Service | Notes |
|---|---|
| **VPC** | Regional — spans all AZs in the region |
| **S3** (data) | Bucket data stored in the region you select |
| **Lambda** | Functions are regional |
| **API Gateway** | Regional by default; edge-optimized uses CloudFront |
| **ALB / NLB / GLB** | Regional — routes across AZs |
| **ECS / EKS / ECR** | Cluster and registry are regional |
| **DynamoDB** | Tables are regional (Global Tables span regions) |
| **Aurora** | Cluster is regional; Global Database spans regions |
| **RDS** (service) | Regional service; individual instances run in a specific AZ |
| **ElastiCache** | Cluster is regional; individual nodes are AZ-specific |
| **Redshift** | Cluster is regional (single-AZ by default); Multi-AZ option available |
| **Kinesis** | Streams and Firehose are regional |
| **SNS / SQS / EventBridge** | Topics, queues, and event buses are regional |
| **CloudWatch** | Metrics, logs, alarms are regional |
| **CloudTrail** | Trails are regional (multi-region trail delivers all to one S3 bucket) |
| **CloudFormation** | Stacks are regional |
| **Step Functions** | State machines are regional |
| **Elastic Beanstalk** | Environments are regional |
| **EFS** | File system is regional — spans all AZs; mount targets are per AZ |
| **FSx** | File systems are regional |
| **EBS Snapshots** | Stored in the region (can be copied cross-region) |
| **AMI** | Regional (can be copied cross-region) |
| **ACM** | Certificates are regional — exception: CloudFront requires `us-east-1` |
| **KMS** | Keys are regional — cannot be used across regions directly |
| **Secrets Manager** | Secrets are regional |
| **Systems Manager** | Regional |
| **Transit Gateway** | Regional hub; inter-region TGW peering connects across regions |
| **Internet Gateway** | Attached to a VPC (regional) |
| **VPN Gateway (VGW)** | Attached to a VPC (regional) |
| **Security Groups** | Scoped to a VPC (regional) |
| **NACLs** | Scoped to a VPC subnet (regional) |
| **Athena / Glue / EMR** | Regional |
| **CodePipeline / CodeBuild / CodeDeploy** | Regional |

---

### AZ-Specific (scoped to a single Availability Zone)
| Service | Notes |
|---|---|
| **EC2 instance** | Runs in one AZ — if AZ fails, instance is lost |
| **EBS volume** | Tied to one AZ — cannot attach to an instance in a different AZ |
| **Subnet** | Belongs to one AZ |
| **NAT Gateway** | Created in a specific AZ — deploy one per AZ for HA |
| **RDS instance** | Runs in one AZ; Multi-AZ adds a standby in a second AZ |
| **ElastiCache node** | Individual nodes are AZ-specific |
| **EFS mount target** | One mount target per AZ — the EFS file system itself is regional |
| **Redshift node** | Single-AZ cluster; data in one AZ unless Multi-AZ enabled |

## Regions
AWS operates its cloud services across geographic locations called **Regions**. Each Region is a separate geographic area consisting of multiple **Availability Zones (AZs)**, which are isolated locations within the Region.
**Each region consist of minimum of 3 AZs.**
- As of April 2025, AWS offers 36 regions and 108 availability zones globally. Each region has at least 2 availability zones, and some regions have up to 6 availability zones. This allows for high availability and fault tolerance in applications deployed on AWS.

### Key Features:
- **Data sovereignty and compliance**: Users can choose where their data is stored and processed.
- **Low latency**: Optimized for users in the Region.
- **Independent failure zones**: Ensures high availability.

**Examples**: 
- `us-east-1` (North Virginia)
- `eu-west-1` (Ireland)

---

## Availability Zones (AZs)
**Availability Zones** are isolated locations within a Region. **Each AZ consists of one or more data centers** and is interconnected with other AZs in the Region using low-latency, high-bandwidth networks.

### Key Features:
- Independent power, cooling, and physical security.
- Low-latency connections between AZs within a Region.
- Ideal for running highly available applications and databases.

**Example**: 
- A Region like **US East (N. Virginia)** might have 6 AZs (e.g., `us-east-1a`, `us-east-1b`, etc.).
- Applications can be deployed across multiple AZs for redundancy.

---

## Edge Locations
AWS uses **Edge Locations** for services like **Amazon CloudFront (CDN)**, **Route 53 (DNS service)**, and **AWS Lambda@Edge**. These are data centers closer to end-users, enabling faster delivery of content and services by caching data and reducing latency.

### Key Features:
- Faster content delivery.
- Supports services like **CloudFront**, **Route 53**, and **Lambda@Edge**.
- Suitable for applications requiring low latency and high transfer speeds.

---

## Local Zones
**AWS Local Zones** extend AWS Regions to large metropolitan areas, providing lower latency access for applications requiring single-digit millisecond latencies.

**Examples**: 
- AWS Local Zones are available in cities like **Los Angeles**, **Boston**, and **Chicago**.
- In India, AWS Local Zones are in cities like **Mumbai** and **Pune**.

---

## Wavelength
**AWS Wavelength** embeds AWS compute and storage services within **telecommunications providers' data centers at the edge of 5G networks** — application traffic reaches your servers without leaving the mobile carrier's network, eliminating internet hops and achieving **single-digit millisecond latency**.

### Key Concepts:
- **Wavelength Zones**: AWS infrastructure deployments inside telecom providers' 5G data centers — appear as an extension of an AWS Region (like an AZ, but at the carrier edge).
- Traffic from 5G devices goes directly to Wavelength Zones over the carrier network — **never traverses the public internet**.
- You can deploy EC2 instances, EKS node groups, and ECS tasks **into Wavelength Zones** to place compute at the 5G edge.

### Use Cases:
- Real-time AI/ML inference at the edge (e.g., medical diagnostics, autonomous vehicles)
- **IoT**, **AR/VR**, real-time gaming, live video streaming
- Any application requiring ultra-low latency to mobile 5G users

### EKS + Wavelength:
- Create EKS **node groups in Wavelength Zones** to run Kubernetes workloads at the 5G edge.
- Worker nodes run inside the Wavelength Zone; the EKS control plane remains in the parent AWS Region.

---

## AWS Global Network
The **AWS Global Network** is a private fiber-optic network connecting AWS Regions, Availability Zones, and Edge Locations. It ensures high-speed, low-latency connectivity between AWS services and customers.

### Key Features:
- High-speed, low-latency connections between AWS services.
- Global backbone network for data transfer.
- Ensures security and reliability for data in transit.
