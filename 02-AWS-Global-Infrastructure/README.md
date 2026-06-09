# AWS Global Infrastructure Concepts

The AWS Global Infrastructure is a combination of data centers, networks, and technologies distributed globally to deliver a fast, flexible, and secure cloud experience.

## AWS Service Scopes
| Scope | Resources |
|---|---|
| **Global** | <pre>EFS(Multi-region)<br>Global Accelerator</pre> |
| **Regional** | <pre>AWS CloudShell(console)<br>EC2,EBS,Snapshot,AMI,Load Balancer<br>S3<br></pre> |

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
