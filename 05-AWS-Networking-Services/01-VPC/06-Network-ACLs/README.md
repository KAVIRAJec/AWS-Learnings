## Network ACLs (Access Control Lists)

A **Network ACL** is a stateless firewall that controls inbound and outbound traffic at the **subnet level** — every packet entering or leaving the subnet is checked.

- **Stateless** — inbound and outbound are evaluated independently. If you allow an inbound request, you must **also** explicitly allow the outbound response.
- Rules support both **Allow and Deny** — unlike Security Groups which are allow-only.
- Rules(1-32766) are evaluated in **ascending number order** (lowest first) — first match wins, evaluation stops. The last rule (*) is a **catch-all rule** that matches any traffic not previously matched and is typically set to **DENY** by default.
- Each subnet must be associated with **exactly one NACL**. If none is assigned, the default NACL applies.
- Changes apply to **all instances** in the associated subnet.

---

## Rule Evaluation Order

```
Rule #100  ALLOW  TCP  0.0.0.0/0  port 443
Rule #200  ALLOW  TCP  0.0.0.0/0  port 80
Rule #300  DENY   TCP  1.2.3.4/32  port 80   ← too late, #200 already matched
Rule *     DENY   ALL  0.0.0.0/0            ← default catch-all deny
```

> Place lower rule numbers for more specific or higher-priority rules. Rule `*` (implicit deny) always runs last.

---

## Inbound Request Flow (someone calls your EC2)

```
Internet
    │
    ▼
Internet Gateway
    │
    ▼
NACL — checks inbound rules (rule # order, first match wins)
    │  allowed ✓
    ▼
Security Group — checks inbound rules
    │  allowed ✓
    ▼
EC2 Instance  ←── request arrives, processed
    │
    │  response
    ▼
Security Group — auto-allows (stateful)
    │
    ▼
NACL — checks outbound rules  ← MUST explicitly allow (stateless)
    │  e.g. allow ephemeral ports 1024–65535 for response traffic
    ▼
Internet Gateway → back to caller
```

---

## Outbound Request Flow (your EC2 calls the internet)

```
EC2 Instance  ── initiates request (e.g. curl google.com on port 443)
    │
    ▼
Security Group — checks outbound rules
    │  allowed ✓
    ▼
NACL — checks outbound rules (rule # order)
    │  allowed ✓
    ▼
Internet Gateway → Internet
    │
    │  response comes back on ephemeral port (e.g. 32768–60999)
    ▼
NACL — checks inbound rules  ← MUST allow ephemeral ports inbound (stateless)
    │
    ▼
Security Group — auto-allows (stateful — remembers outbound request)
    │
    ▼
EC2 Instance  ←── response received
```

---

## Ephemeral Ports

When a client connects to a server, the **response** is sent back to a random high port on the client (ephemeral port) — not the same port the request was sent on.

Since NACLs are stateless, **forgetting to allow ephemeral ports** is the most common reason responses get blocked at the NACL even when the inbound rule looks correct.

**OS and service-specific ephemeral port ranges:**

| Client / Source | Ephemeral Port Range |
|---|---|
| **Amazon Linux kernel** | 32768 – 61000 |
| **Windows (through Server 2003)** | 1025 – 5000 |
| **Windows Server 2008 and later** | 49152 – 65535 |
| **Elastic Load Balancing (ELB)** | 1024 – 65535 |
| **NAT Gateway** | 1024 – 65535 |
| **AWS Lambda** | 1024 – 65535 |

> AWS recommends allowing `1024–65535` outbound on NACLs to safely cover all client OS ranges. Use `32768–65535` if you only need to cover Linux-based clients.

**When your EC2 is the SERVER (e.g. web server on port 443):**
- NACL must allow **inbound** on port 443 (client request arrives).
- NACL must allow **outbound** on ephemeral ports 32768–65535 (response sent back to the Linux client's random port).
- Security Groups are stateful — only the inbound rule on port 443 is needed; the outbound response is auto-allowed.

**When your EC2 is the CLIENT (e.g. calling an external API):**
- NACL must allow **outbound** on the destination port (e.g. 443).
- NACL must allow **inbound** on ephemeral ports 32768–65535 (response arrives on a random local port).

---

## Default NACL vs Custom NACL

| | Default NACL | Custom NACL |
|---|---|---|
| **Created** | Auto-created with VPC | You create it |
| **Inbound** | Allow all | Deny all (until you add rules) |
| **Outbound** | Allow all | Deny all (until you add rules) |
| **Subnets** | All subnets with no explicit NACL | Only subnets you associate |

---

## Security Group vs NACL

| | Security Group | Network ACL |
|---|---|---|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow + Deny |
| **Rule evaluation** | All rules together | Lowest number first, first match wins |
| **Response traffic** | Auto-allowed | Must explicitly allow ephemeral ports |
| **Default** | Deny all inbound | Allow all (default NACL) |
