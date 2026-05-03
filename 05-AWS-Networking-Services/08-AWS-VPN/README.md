## AWS VPN (Virtual Private Network)

AWS VPN creates **encrypted tunnels** between your on-premises network and AWS over the public internet. It's the quickest way to set up hybrid connectivity without dedicated physical links.

Two types: **Site-to-Site VPN** (network to network) and **Client VPN** (user to network).

---

## Site-to-Site VPN

Connects your **on-premises data center or branch office** to an AWS VPC — encrypted using **IPsec** tunnels over the internet.

### Components

**Virtual Private Gateway (VGW):**
- The VPN concentrator on the **AWS side** — attached to your VPC.
- One VGW per VPC.
- After attaching, add a route(route propagation) in the VPC route table pointing on-premises CIDR → VGW.
- If need to ping EC2 instance from on-premises then make sure to add ICMP protocol on the inbound of the security group.

**Customer Gateway (CGW):**
- Represents your **on-premises side** — a physical or software VPN appliance (Cisco, Fortinet, pfSense, etc.).
- You register the CGW in AWS with the device's public IP and routing type.

### Tunnel Setup

Each Site-to-Site VPN connection creates **two IPsec tunnels** — both active, for redundancy. If one tunnel fails, traffic automatically fails over to the second.

```
On-premises Network
    │
    │  IPsec tunnel 1 (active)
    │  IPsec tunnel 2 (standby/active)
    ▼
Customer Gateway (CGW)  ←── your router/firewall
    │
    │  encrypted over public internet
    ▼
Virtual Private Gateway (VGW)  ←── attached to VPC
    │
    ▼
VPC (private subnets, EC2, RDS, etc.)
```

### Routing: Static vs Dynamic (BGP)

| | Static Routing | Dynamic Routing (BGP) |
|---|---|---|
| **How** | You manually enter on-premises CIDRs in AWS | BGP automatically exchanges routes between CGW and VGW |
| **Best for** | Simple setups, fixed networks | Large networks, multiple CIDRs, automatic failover |
| **Route updates** | Manual — you update if network changes | Automatic — BGP propagates new routes |
| **Recommended** | No | Yes — BGP is preferred |

With BGP, enable **route propagation** on the VPC route table — routes from on-premises are automatically added without manual entries.

### Accelerated Site-to-Site VPN

By default, VPN traffic travels over the public internet — latency can vary. With **Accelerated VPN**, traffic enters AWS's global network at the nearest **AWS Global Accelerator edge location**, reducing latency and improving stability.

- Uses **AWS Global Accelerator** under the hood.
- Requires a **Transit Gateway** (not VGW) on the AWS side.
- Additional cost on top of standard VPN pricing.

---

## Client VPN

**AWS Client VPN** lets individual users (developers, remote employees) securely connect to AWS resources or on-premises networks from any location using an **OpenVPN-based client**.

- Users connect via SSL/TLS (TCP 443) — works through most firewalls and NAT.
- Connections terminate at a **Client VPN Endpoint** — associated with a VPC subnet.
- Supports **split-tunnel** (only VPC-bound traffic goes through VPN, internet traffic stays local) or **full-tunnel** (all traffic routes through VPN).

```
User's Laptop (OpenVPN client)
    │
    │  SSL/TLS over port 443
    ▼
Client VPN Endpoint (associated with VPC subnet)
    │
    ▼
VPC resources (EC2, RDS, etc.) or on-premises via VGW
```

### Authentication Options

- **Active Directory (AWS Managed AD or on-premises AD)** — users log in with corporate credentials.
- **Certificate-based (mutual TLS)** — client and server both present certificates.
- **SSO (SAML 2.0)** — integrate with identity providers like Okta, Azure AD.

### Split Tunnel vs Full Tunnel

| | Split Tunnel | Full Tunnel |
|---|---|---|
| **VPC traffic** | Through VPN | Through VPN |
| **Internet traffic** | Direct (local network) | Through VPN |
| **Bandwidth usage** | Lower — only VPC traffic uses VPN | Higher — all traffic through VPN |
| **Use case** | Dev access to AWS resources | Compliance — all traffic must be inspected |

---

## AWS VPN CloudHub

**AWS VPN CloudHub** uses a single **Virtual Private Gateway (VGW)** as a hub to connect multiple on-premises branch offices — branch offices can communicate with each other through the VGW, not just with the VPC.

- Hub-and-spoke model — VGW is the hub, each branch office is a spoke.
- Each branch has its own **Customer Gateway (CGW)** and Site-to-Site VPN connection to the same VGW.
- Branch-to-branch traffic flows: `Branch A → VGW → Branch B` (through AWS, not the internet directly).
- Requires **BGP(Border Gateway Protocol)** — each CGW must advertise unique ASN (Autonomous System Number) and non-overlapping CIDRs.
- Low-cost option for branch interconnectivity — pay only for VPN connection hours and data transfer.
- Can be used as **primary connectivity** or as a **backup** to Direct Connect.

```
Branch Office A (CGW-A)
        │  IPsec VPN
        ▼
    ┌───────────────────────────┐
    │  Virtual Private Gateway  │  ←── hub
    │         (VGW)             │
    └───────────────────────────┘
        │              │
   IPsec VPN       IPsec VPN
        │              │
Branch Office B    Branch Office C
   (CGW-B)            (CGW-C)

Branch A ↔ Branch B ↔ Branch C  (all communicate via VGW)
VPC also reachable from all branches
```

> CloudHub is not a separate AWS service — it's a **topology pattern** using standard Site-to-Site VPN with multiple CGWs connected to one VGW.

---

## Site-to-Site VPN vs Client VPN vs Direct Connect

| | Site-to-Site VPN | Client VPN | Direct Connect |
|---|---|---|---|
| **Connects** | Network ↔ VPC | User device ↔ VPC | Data center ↔ VPC |
| **Encryption** | IPsec | SSL/TLS | Not encrypted by default |
| **Speed** | Up to 1.25 Gbps per tunnel | Depends on user connection | 1 Gbps – 100 Gbps dedicated |
| **Latency** | Variable (internet) | Variable (internet) | Consistent (dedicated line) |
| **Setup time** | Minutes | Minutes | Weeks (physical provisioning) |
| **Cost** | Low | Per connection-hour | High (port + data transfer) |
| **Best for** | Hybrid connectivity, quick setup | Remote user access | High throughput, consistent latency |
