## AWS VPC (Virtual Private Cloud)

A Virtual Private Cloud (VPC) is a secure and isolated network that you can create within the AWS cloud. It allows you to define your own network topology and complete control over it, including IP address ranges, subnets, route tables, and network gateways.

- You can launch AWS resources, such as EC2 instances, RDS databases, and Lambda functions, within your VPC.
- VPC limit per region is 5(soft limit) & CIDR limit per VPC is 5.
- VPC contains list of IP addresses and it can be divided into subnets.

---

## AWS Default VPC

Every AWS account gets a **default VPC** in each region — pre-configured and ready to use.
- **Default VPC CIDR**: `172.31.0.0/16` (65,536 IPs)
- **Default subnets**: One `/20` subnet per Availability Zone (4,096 IPs, 4,091 usable)
- Default subnets are **public** — instances launched here get a public IP automatically.
- Comes with an Internet Gateway, route table, and default security group pre-attached.

**Common private IP ranges used for custom VPCs:**

| Range             | CIDR       | Max IPs    |
|-------------------|------------|------------|
| `10.0.0.0`        | /8 to /28  | up to 16M  |
| `172.16.0.0`      | /12 to /28 | up to 1M   |
| `192.168.0.0`     | /16 to /28 | up to 65K  |

VPC CIDR must be between **/16** (max, 65,536 IPs) and **/28** (min, 16 IPs).

---

## VPC Components

| # | Topic | Description |
|---|-------|-------------|
| 01 | [Subnets](./01-Subnets/README.md) | Public and private subnet segments within a VPC |
| 02 | [Internet Gateway](./02-Internet-Gateway/README.md) | Enables communication between VPC instances and the internet |
| 03 | [Route Tables](./03-Route-Tables/README.md) | Controls traffic routing within the VPC and to the internet |
| 04 | [NAT Gateway](./04-NAT-Gateway/README.md) | Allows private subnet instances to initiate outbound internet traffic |
| 05 | [Security Groups](./05-Security-Groups/README.md) | Stateful instance-level firewall for inbound/outbound traffic |
| 06 | [Network ACLs](./06-Network-ACLs/README.md) | Stateless subnet-level firewall for inbound/outbound traffic |
| 07 | [VPC Peering](./07-VPC-Peering/README.md) | Private connectivity between two VPCs across accounts or regions |
| 08 | [VPC Endpoints](./08-VPC-Endpoints/README.md) | Private access to AWS services without internet gateway or NAT |
| 09 | [VPC Flow Logs](./09-VPC-Flow-Logs/README.md) | Captures IP traffic metadata for monitoring and troubleshooting |
| 10 | [VPC Traffic Mirroring](./10-VPC-Traffic-Mirroring/README.md) | Captures full packet data from ENIs for security analysis |
| 11 | [Bastion Host](./11-Bastion-Host/README.md) | Jump server in public subnet for SSH access to private subnet instances |
| 12 | [IPv6](./12-IPv6/README.md) | Dual-stack VPC, IPv6 CIDR assignment, and Egress-Only Internet Gateway |

---
