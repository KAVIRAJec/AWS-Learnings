## AWS Network Firewall

**AWS Network Firewall** is a managed, stateful network firewall and intrusion detection/prevention system (IDS/IPS) deployed **inside your VPC** — inspects and filters traffic at Layers 3–7 across all ports and protocols.

- Deployed into a **dedicated firewall subnet** in each AZ.
- Inspects traffic flowing **between subnets, to/from the internet, and to/from on-premises** (via VPN/Direct Connect).
- Supports both **stateless** (fast, header-only) and **stateful** (deep packet inspection, session-aware) rule engines.
- Managed by **AWS Firewall Manager** for centralized deployment across an AWS Organization.

---

## How It Works

Network Firewall sits in the traffic path — you redirect traffic through it by updating route tables.

```
Internet
    │
    ▼
Internet Gateway
    │
    ▼  (route table sends traffic to firewall endpoint)
Network Firewall (firewall subnet)
    │   stateless rules → stateful rules → IDS/IPS → allow/drop
    ▼
Application Subnet (EC2, ECS, etc.)
```

Traffic flow is controlled by adding the **firewall endpoint** as a route target — similar to how you point `0.0.0.0/0` to an IGW or NAT Gateway.

---

## Components

**Firewall:**
- The top-level resource — deployed into your VPC, spans one or more AZs.
- Each AZ gets a **firewall endpoint** (an ENI) in the firewall subnet.

**Firewall Policy:**
- Attached to a firewall — defines the rule groups and their order of evaluation.
- One policy per firewall, but a policy can be shared across multiple firewalls.

**Rule Groups:**
- Collections of rules — either **stateless** or **stateful**.
- Can be AWS managed (e.g. AWS threat intelligence feeds) or custom.

---

## Rule Types

### Stateless Rules
- Evaluated first — fast, per-packet inspection (no session awareness).
- Match on: source/destination IP, port, protocol, TCP flags.
- Actions: **Pass** (skip stateful), **Drop**, **Forward to stateful engine**.
- Similar to NACLs — evaluated in priority order, first match wins.

### Stateful Rules
- Session-aware — tracks connection state.
- Three formats:
  - **Standard rules** — 5-tuple (source IP, dest IP, source port, dest port, protocol) with actions.
  - **Suricata-compatible rules** — industry-standard IDS/IPS rule format. Full deep packet inspection, regex matching on payload, TLS SNI inspection.
  - **Domain list rules** — allow or block traffic to specific domains (e.g. block `*.malware.com`, allow only `*.amazonaws.com`).

```
Domain list example:
  DENY  *.example-malware.com
  ALLOW .amazonaws.com
  ALLOW .s3.amazonaws.com
```

---

## Deployment Architecture

Network Firewall is typically placed at the **VPC ingress/egress point** — all traffic in and out passes through it.

**Single VPC:**
```
Internet Gateway
    │
    │  IGW route table: 0.0.0.0/0 → Firewall Endpoint
    ▼
Firewall Subnet  (Network Firewall endpoint)
    │
    │  Firewall route table: 0.0.0.0/0 → IGW, 10.0.1.0/24 → local
    ▼
Application Subnet
```

**Centralized with Transit Gateway (inspect all VPC traffic):**
```
VPC-A  ──┐
VPC-B  ──┤── Transit Gateway ──► Inspection VPC (Network Firewall) ──► Internet
VPC-C  ──┘
```
All outbound traffic from spoke VPCs routes through the inspection VPC before reaching the internet.

---

## Logging

Network Firewall can log to **S3**, **CloudWatch Logs**, or **Kinesis Firehose**:

| Log Type | What it captures |
|---|---|
| **Alert logs** | Traffic that matched a rule with alert/drop action |
| **Flow logs** | All traffic flows (similar to VPC Flow Logs but from firewall) |
| **TLS logs** | TLS handshake metadata for inspected encrypted traffic |

---

## Network Firewall vs WAF vs Security Groups vs NACLs

| | Network Firewall | WAF | Security Groups | NACLs |
|---|---|---|---|---|
| **Layer** | L3–L7 | L7 (HTTP/HTTPS only) | L3–L4 | L3–L4 |
| **Scope** | VPC-level (all traffic) | CloudFront, ALB, API GW | Instance-level | Subnet-level |
| **Stateful** | Yes (stateful engine) | Yes | Yes | No |
| **IDS/IPS** | Yes — Suricata rules | No | No | No |
| **Domain filtering** | Yes | Yes (host header) | No | No |
| **Protocol support** | All (TCP, UDP, ICMP, etc.) | HTTP/HTTPS only | All | All |
| **Managed by Firewall Manager** | Yes | Yes | Yes | No |
| **Cost** | Per hour + per GB | Per rule + per request | Free | Free |
