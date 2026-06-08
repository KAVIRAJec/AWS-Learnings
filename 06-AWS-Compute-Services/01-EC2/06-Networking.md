## ENI (Elastic Network Interface)
- An ENI is a virtual network interface that can be attached to an EC2 instance. It provides a way to manage network connectivity and security for your instances.
- Same as a physical network interface card (NIC) in a physical server or VEth in Docker.
- An ENI can have 1 primary private IP address and multiple secondary private IP addresses, 1 Elastic IP address, 1 public IP address, MAC address, and multiple security groups.
- ENI is specific to a single AZ — can only be moved between instances **in the same AZ**.

**Primary vs Secondary ENI:**

| | Primary ENI (`eth0`) | Secondary ENI (`eth1`, `eth2`…) |
|---|---|---|
| **Created by** | AWS automatically on instance launch | You create manually |
| **Detachable** | **No — cannot be detached** from a running or stopped instance | Yes — can be detached and re-attached to another instance |
| **Deleted on termination** | Yes (by default) | Controlled by you |

**Failover pattern using secondary ENI:**

When an application's domain name points to the **private IP of a secondary ENI**, you can move that ENI to a standby instance if the primary fails — traffic resumes with no DNS changes, no route table updates.

```
Normal:
  Domain → private IP (secondary ENI) → attached to Instance A (primary)

On failure:
  Detach secondary ENI from Instance A
  Attach secondary ENI to Instance B (hot standby)
  Domain → same private IP → now Instance B handles traffic
```

- The secondary ENI **retains its private IP, Elastic IP, and MAC address** when moved — no changes needed anywhere else.
- Users experience only a brief interruption during the detach/attach window.
- This pattern works for critical services (databases, NAT instances, dashboards) where you want quick failover without DNS propagation delay.

![alt text](image-3.png)

---

## ENA (Elastic Network Adapter)
- **Enhanced networking** interface for EC2 — delivers high bandwidth (up to 100 Gbps), high packet-per-second (PPS) performance, and low latency compared to a standard ENI.
- No extra cost — enabled by default on most modern instance types (Nitro-based instances).
- **EFA (Elastic Fabric Adapter)**: A specialized ENA for **HPC (High Performance Computing) and ML** workloads — uses OS-bypass to let applications communicate directly with the hardware, skipping the OS kernel for ultra-low latency. Used with MPI (Message Passing Interface) workloads like distributed ML training and tightly coupled simulations. **OS-bypass is not supported on Windows instances** — attaching an EFA to a Windows instance makes it behave as a regular ENA without the EFA capabilities.

| | ENI | ENA | EFA |
|---|---|---|---|
| **Use case** | General networking | High bandwidth / low latency | HPC, distributed ML, MPI |
| **OS bypass** | No | No | Yes |
| **Max bandwidth** | Varies | Up to 100 Gbps | Up to 100 Gbps |

---

## SSM Session Manager

**SSM Session Manager** is the modern, keyless way to access EC2 instances — open a shell session directly from the AWS console, CLI, or SDK without SSH, without a Bastion Host, and without opening port 22.

**Requirements:**
- EC2 instance must have the **SSM Agent** installed and running (pre-installed on Amazon Linux 2, Amazon Linux 2023, Ubuntu 20.04+, Windows Server 2016+).
- Instance must have an **IAM Instance Profile** with the `AmazonSSMManagedInstanceCore` policy attached.
- No inbound security group rules needed — the agent initiates an outbound HTTPS connection to the SSM endpoint.

```
Your browser / AWS CLI
        │
        │  HTTPS (port 443) — outbound only from EC2
        ▼
AWS Systems Manager endpoint
        │
        ▼
SSM Agent (running on EC2)  ←── no inbound port 22 needed
        │
        ▼
Shell session on EC2
```

**Key benefits:**
- **No SSH keys** — access is controlled entirely by IAM permissions.
- **No open ports** — security group has zero inbound rules needed.
- **Full audit trail** — every session command is logged to **CloudTrail** and optionally to **S3** or **CloudWatch Logs**.
- Works for instances in **private subnets** with no public IP — as long as the agent can reach the SSM endpoint (via VPC Endpoint or NAT Gateway).
- Supports **port forwarding** — tunnel RDS, Redis, or any internal service to your local machine without a Bastion.

**SSM Session Manager vs Bastion Host:**

| | SSM Session Manager | Bastion Host |
|---|---|---|
| **Access method** | HTTPS via SSM Agent — no open ports | SSH over port 22 |
| **SSH keys** | Not needed — IAM controls access | Required |
| **Public IP on EC2** | Not needed | Required on bastion |
| **Private subnet access** | Yes | Yes (via bastion in public subnet) |
| **Audit trail** | CloudTrail + S3/CloudWatch Logs | Manual SSH logs |
| **Cost** | Free (SSM is free) | EC2 instance cost |
| **Best for** | Secure, auditable, keyless access | Legacy setups |

---

## EC2 Instance Connect

**EC2 Instance Connect** provides temporary, one-time SSH access to EC2 instances by injecting an **ephemeral public key** — no long-term SSH key pairs needed.

**How it works:**
```
AWS Console / CLI
        │
        │  1. Injects a one-time-use public key into instance metadata (valid for 60 seconds)
        ▼
EC2 Instance (authorized_keys updated temporarily)
        │
        │  2. SSH connection made using the matching private key
        ▼
SSH session established → key expires after 60 seconds
```

**Two modes:**

| | EC2 Instance Connect (direct) | EC2 Instance Connect Endpoint |
|---|---|---|
| **When to use** | Instance has a **public IP** | Instance is in a **private subnet** (no public IP) |
| **Connection path** | Over the internet via public IP | Through a managed VPC endpoint — no internet needed |
| **Port 22 required** | Yes — inbound from EC2 Instance Connect service IP ranges | Yes — but only from the endpoint, not the internet |
| **SSM Agent needed** | No | No |
| **Cost** | Free | Free (data transfer costs apply) |

**Key properties:**
- **Ephemeral keys** — the injected public key is valid for **60 seconds** only. After the session starts, the key is no longer valid for new connections.
- **No static key distribution** — developers never hold a long-term SSH key.
- **Auditable** — every connection is logged in **CloudTrail** (who connected, when, from which IP).
- **Console access** — connect directly from the AWS Management Console without a terminal.
- **Private IP from internet** — not supported. Without Instance Connect Endpoint, you need the instance's public IP. Private IPs are not internet-routable.

> **Instance Connect Endpoint** is specifically for private subnet instances. If the instance already has a public IP, use direct Instance Connect — adding an endpoint for public instances adds unnecessary complexity and cost.

**EC2 Instance Connect vs SSM Session Manager:**

| | EC2 Instance Connect | SSM Session Manager |
|---|---|---|
| **Protocol** | SSH (port 22) | HTTPS (port 443) |
| **Key management** | Ephemeral key injected at connect time | No keys — IAM controls access |
| **SSM Agent** | Not needed | Required |
| **Private subnet** | Needs Instance Connect Endpoint | Works natively (no endpoint needed) |
| **Console access** | Yes | Yes |
| **Best for** | Temporary SSH with audit trail, no key distribution | Keyless, portless shell access to any instance |
