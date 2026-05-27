## Security Groups

A **Security Group** is a virtual firewall that controls inbound and outbound traffic at the **instance level** (EC2, RDS, Lambda in VPC, etc.).

- **Stateful** — if an inbound request is allowed, the response is automatically allowed outbound, and vice versa. No need to define both directions.
- Rules are **allow-only** — you cannot write a deny rule. Everything not explicitly allowed is denied by default.
- An instance can have **multiple security groups** — all rules are evaluated together (union).
- Changes take effect **immediately**.

---

## Source / Destination in Rules

When writing a Security Group rule, the **Source** (inbound) or **Destination** (outbound) tells AWS where the traffic comes from or goes to. You can specify it in 4 ways:

| Type | Example | When to use |
|---|---|---|
| **IP / CIDR** | `203.0.113.5/32`, `10.0.0.0/16` | Allow a specific IP or a subnet range |
| **Anywhere (all traffic)** | `0.0.0.0/0` (IPv4), `::/0` (IPv6) | Open to the entire internet or all outbound |
| **Another Security Group** | `sg-0abc1234` | Allow traffic only from instances that have this SG attached — no need to know their IPs |
| **Prefix List** | `pl-xxxxxxxx` | Allow traffic to/from an AWS managed service (e.g., S3, DynamoDB via Gateway Endpoint) |

---

## Inbound Request Flow (someone calls your EC2)

```
Internet
    │
    ▼
Internet Gateway
    │
    ▼
NACL (subnet level) — checks inbound rules
    │  allowed ✓
    ▼
Security Group — checks inbound rules
    │  allowed ✓
    ▼
EC2 Instance  ←── request arrives, processed
    │
    │  response (STATEFUL — SG auto-allows return traffic)
    ▼
Security Group — no outbound check needed (stateful)
    │
    ▼
NACL — checks outbound rules (stateless — must explicitly allow)
    │
    ▼
Internet Gateway → back to caller
```

---

## Outbound Request Flow (your EC2 calls the internet)

```
EC2 Instance  ── initiates request (e.g. curl google.com)
    │
    ▼
Security Group — checks outbound rules
    │  allowed ✓
    ▼
NACL (subnet level) — checks outbound rules
    │  allowed ✓
    ▼
Internet Gateway → Internet (e.g. Google)
    │
    │  response comes back
    ▼
NACL — checks inbound rules (stateless — must explicitly allow)
    │
    ▼
Security Group — no inbound check needed (stateful — remembers the outbound request)
    │
    ▼
EC2 Instance  ←── response received
```

---

## Default Rules

**Default Security Group (auto-created with VPC):**
- Inbound: allow all traffic **from the same security group** (instances talk to each other)
- Outbound: allow all traffic to anywhere (`0.0.0.0/0`)

**Custom Security Group (when you create a new one):**
- Inbound: **nothing allowed** (deny all by default)
- Outbound: allow all traffic to anywhere (`0.0.0.0/0`)

---

## Security Group vs NACL

| | Security Group | Network ACL |
|---|---|---|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow + Deny |
| **Rule evaluation** | All rules evaluated together | Rules evaluated in number order (lowest first) |
| **Default inbound** | Deny all | Allow all (default NACL) |
| **Response traffic** | Auto-allowed | Must explicitly allow |
