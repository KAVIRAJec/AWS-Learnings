# AWS Global Infrastructure Concepts

The AWS Global Infrastructure is a combination of data centers, networks, and technologies distributed globally to deliver a fast, flexible, and secure cloud experience.

## Regions
AWS operates its cloud services across geographic locations called **Regions**. Each Region is a separate geographic area consisting of multiple **Availability Zones (AZs)**, which are isolated locations within the Region.
**Each region consist of minimum of 3 AZs.**

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
**AWS Wavelength** integrates AWS infrastructure with telecommunications networks, enabling developers to build applications requiring ultra-low latency and high bandwidth. It is designed for mobile edge computing and integrates with **5G networks**.

### Key Features:
- Ideal for applications like **IoT**, **AR/VR**, and **real-time gaming**.
- Provides ultra-low latency by running applications on telecom networks.

---

## AWS Global Network
The **AWS Global Network** is a private fiber-optic network connecting AWS Regions, Availability Zones, and Edge Locations. It ensures high-speed, low-latency connectivity between AWS services and customers.

### Key Features:
- High-speed, low-latency connections between AWS services.
- Global backbone network for data transfer.
- Ensures security and reliability for data in transit.

## Interview Questions:
1. **AWS Availability Zones**  
    As of April 2025, AWS offers 36 regions and 108 availability zones globally. Each region has at least 2 availability zones, and some regions have up to 6 availability zones. This allows for high availability and fault tolerance in applications deployed on AWS.