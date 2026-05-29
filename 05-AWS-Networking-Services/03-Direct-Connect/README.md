## AWS Direct Connect (DX)

**AWS Direct Connect** is a dedicated, private physical network connection from your on-premises data center to AWS — traffic never touches the public internet.

- Consistent network performance — no internet congestion or variable latency.
- Supports **50 Mbps to 100 Gbps** bandwidth.
- **Not encrypted by default** — use IPsec VPN over Direct Connect if encryption is needed.
- Takes **weeks to provision** — physical cable must be run to an AWS Direct Connect location (colocation facility).

---

## Connection Types

**Dedicated Connection:**
- A physical port at an AWS Direct Connect location exclusively for you.
- Speeds: **1 Gbps, 10 Gbps, 100 Gbps**.
- You work directly with AWS to provision it.

**Hosted Connection:**
- A connection provisioned by an **AWS Direct Connect Partner** — they share their physical port and give you a slice of bandwidth.
- Speeds: **50 Mbps to 10 Gbps** (more flexible, smaller increments).
- Faster to set up than a dedicated connection — partner already has the physical link.

---

## Virtual Interfaces (VIFs)

A single Direct Connect physical connection is split into **Virtual Interfaces (VIFs)** — each VIF is a logical connection for a specific purpose:

| VIF Type | Connects to | Use case |
|---|---|---|
| **Private VIF** | VPC (via VGW or Transit Gateway) | Access EC2, RDS, and other VPC resources via private IPs |
| **Public VIF** | AWS public services | Access S3, DynamoDB, SNS, SQS etc. without going through internet |
| **Transit VIF** | AWS Transit Gateway | Connect to multiple VPCs across regions from one DX connection |

```
On-premises Data Center
    │
    │  Physical dedicated line
    ▼
AWS Direct Connect Location (colocation)
    │
    ├── Private VIF ──► VGW ──► VPC (EC2, RDS)
    ├── Public VIF  ──► AWS Public Services (S3, DynamoDB)
    └── Transit VIF ──► Transit Gateway ──► Multiple VPCs
```

---

## Direct Connect Gateway

A **Direct Connect Gateway** allows a single Direct Connect connection to reach **VPCs in multiple AWS regions** — without needing a separate DX connection per region.

```
On-premises
    │
    │  Direct Connect
    ▼
Direct Connect Gateway
    ├──► VPC in us-east-1
    ├──► VPC in eu-west-1
    └──► VPC in ap-south-1
```

- **Global resource** — works with VPCs in any AWS Region (except China Regions).
- One Direct Connect Gateway can be associated with up to **10 VGWs** across regions.
- VPCs connected through the same DX Gateway **cannot communicate with each other** — it's not a transit hub, only on-premises ↔ VPC.

**Multi-region hybrid pattern — shared DX Gateway:**

When you have multiple DX connections (e.g., two on-premises data centers) and VPCs in multiple regions, connect **both DX links to a single shared DX Gateway** and attach each region's VGW to it:

```
On-premises DC-1 ──DX link 1──┐
                               ├──► Direct Connect Gateway (global)
On-premises DC-2 ──DX link 2──┘        │         │         │
                                     VGW        VGW        VGW
                                  us-west-2  eu-central-1  ap-south-1
                                     │           │             │
                                   VPC-1       VPC-2         VPC-3
```

- All on-premises networks can reach all VPCs across regions through one DX Gateway.
- Lowest operational overhead — no per-region DX connections, no complex peering mesh.
- **Does not replace Transit Gateway** — DX Gateway only enables on-premises ↔ VPC routing, not VPC ↔ VPC routing. For VPC-to-VPC routing combine with Transit Gateway (using Transit VIF).

---

## Resiliency

Direct Connect is a **single physical link** — if the cable or port fails, connectivity is lost. AWS recommends:

**High Resiliency** — two Direct Connect connections at the same location:
```
On-premises ──DX connection 1──► AWS DX Location A ──► AWS
            ──DX connection 2──► AWS DX Location A ──► AWS
```

**Maximum Resiliency** — two connections at two separate DX locations:
```
On-premises ──DX connection 1──► AWS DX Location A ──► AWS
            ──DX connection 2──► AWS DX Location B ──► AWS
```

**Backup with Site-to-Site VPN** — use VPN as a cheaper failover when DX goes down:
```
Primary:  On-premises ──Direct Connect──► VPC
Backup:   On-premises ──Site-to-Site VPN (IPsec)──► VPC
```
VPN kicks in automatically via BGP failover if the DX link drops.

---

## Direct Connect vs Site-to-Site VPN

| | Direct Connect | Site-to-Site VPN |
|---|---|---|
| **Connection** | Dedicated physical line | Encrypted tunnel over internet |
| **Bandwidth** | 50 Mbps – 100 Gbps | Up to 1.25 Gbps per tunnel |
| **Latency** | Consistent, low | Variable (internet dependent) |
| **Encryption** | Not by default (add VPN on top) | Yes — IPsec by default |
| **Setup time** | Weeks | Minutes |
| **Cost** | High (port + data transfer) | Low |
| **Best for** | Large data, consistent performance | Quick hybrid setup, backup for DX |
