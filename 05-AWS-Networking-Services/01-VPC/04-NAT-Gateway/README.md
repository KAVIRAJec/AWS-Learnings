## NAT (Network Address Translation)

NAT allows instances in a **private subnet** to initiate outbound traffic to the internet while **blocking inbound connections** from the internet — the internet never knows the private IP, only the NAT's public IP.

AWS offers two ways to do this: **NAT Instance** (old) and **NAT Gateway** (managed, recommended).

---

## NAT Instance

A **NAT Instance** is a regular EC2 instance in a public subnet configured to forward traffic on behalf of private instances.

- Must **disable Source/Destination Check** on the EC2 instance — by default EC2 drops packets where it is not the source or destination; disabling this allows it to forward traffic.
- You manage it: patching, scaling, HA, failover — all on you.
- Can be used as a **Bastion Host** too (dual purpose).
- Uses a Security Group to control traffic.
- Bandwidth limited by the EC2 instance type.

```
Private EC2
    │
    │  (private IP: 10.0.1.5)
    ▼
NAT Instance (public subnet)
    │  replaces source IP 10.0.1.5 → NAT's public IP
    ▼
Internet
```

---

## NAT Gateway

A **NAT Gateway** is AWS's fully managed NAT service — no EC2 to manage, no patching, scales automatically.

- Deployed in a **public subnet** with an **Elastic IP** attached.
- Private subnet route table must point `0.0.0.0/0 → nat-xxxxxxxx`.
- Scales automatically up to **100 Gbps**.
- **Not associated with Security Groups** — traffic control is done via NACLs and the private instance's SG.
- Highly available **within one AZ** — for multi-AZ HA, deploy one NAT Gateway per AZ.

```
Private EC2 (10.0.1.5)
    │
    ▼
NAT Gateway (public subnet, Elastic IP: 52.x.x.x)
    │  replaces source 10.0.1.5 → 52.x.x.x
    ▼
Internet Gateway
    │
    ▼
Internet
```

**Multi-AZ setup** (recommended for production):
```
AZ-A: Private EC2 → NAT Gateway A (public subnet A) → IGW
AZ-B: Private EC2 → NAT Gateway B (public subnet B) → IGW
```
Each AZ has its own NAT Gateway — if one AZ fails, the other is unaffected.

---

## NAT Gateway Types: Public vs Private

| | Public NAT Gateway | Private NAT Gateway |
|---|---|---|
| **Traffic** | Private subnet → Internet | Private subnet → another VPC or on-premises |
| **Elastic IP** | Required | Not required |
| **Use case** | Outbound internet from private subnets | Cross-VPC or hybrid connectivity without internet |
| **Goes through** | Internet Gateway | Transit Gateway or VPC Peering |

---

## Regional NAT Gateway (Centralized NAT)

Instead of deploying one NAT Gateway per AZ, you can use a **single NAT Gateway** in one AZ to serve private subnets across all AZs in the region — this is the **centralized** (regional) pattern.

**Single NAT Gateway for all AZs:**
```
AZ-A: Private EC2 ──────────────────────┐
                                         ▼
AZ-B: Private EC2 ──────► NAT Gateway (AZ-A, public subnet) → IGW → Internet
                                         ▲
AZ-C: Private EC2 ──────────────────────┘
```

Route tables in AZ-B and AZ-C point to the NAT Gateway sitting in AZ-A.

**Tradeoffs:**

| | Centralized (one NAT GW) | Per-AZ (one NAT GW each) |
|---|---|---|
| **Cost** | Lower — one NAT GW | Higher — multiple NAT GWs |
| **Cross-AZ charges** | Yes — AZ-B/C traffic crosses AZs to reach AZ-A NAT GW | No — each AZ routes to its own NAT GW |
| **Fault tolerance** | If AZ-A fails, all AZs lose internet | Each AZ is independent |
| **Best for** | Dev/test environments, cost-sensitive setups | Production workloads |

> Cross-AZ data transfer costs can often **cancel out** the savings from using one NAT Gateway — measure before choosing centralized for production.

---

## NAT Instance vs NAT Gateway

| | NAT Instance | NAT Gateway |
|---|---|---|
| **Type** | Self-managed EC2 | AWS managed service |
| **Availability** | Manual (you set up HA) | High available within AZ |
| **Bandwidth** | Limited by instance type | Up to 100 Gbps (auto-scales) |
| **Security Groups** | Yes — applied to instance | No — use NACLs instead |
| **Source/Dest Check** | Must disable manually | Not applicable |
| **Cost** | EC2 instance + data | Per hour + per GB data |
| **Bastion Host** | Can double as one | Cannot |
| **Recommended** | Legacy only | Yes — use this |
