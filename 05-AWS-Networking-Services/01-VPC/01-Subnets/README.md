## Subnets

A **subnet** is a subdivision of a VPC's IP range — you place your AWS resources (EC2, RDS, Lambda, etc.) inside subnets.

**Public subnet** — has a route to an Internet Gateway → instances can send/receive traffic from the internet.
**Private subnet** — no direct route to IGW → outbound-only internet access via NAT Gateway; no inbound from internet.

- Each subnet lives in **exactly one AZ** — it cannot span AZs.
- A VPC can have subnets across **multiple AZs** for high availability.
- Subnet CIDR must be a subset of the VPC CIDR and cannot overlap with other subnets.

---

## CIDR (Classless Inter-Domain Routing)

CIDR defines a block of IPs: `IP / prefix`  — prefix = how many bits are **fixed** (network part), the rest are **free** (host part).

```
10.0.0.0 / 16
│            │
│            └── 16 bits fixed → remaining 16 bits free for hosts
└─────────────── starting IP
```

An IP is 32 bits split into 4 octets (each 8 bits, ranges 0–255):

```
  10   .   0   .   0   .   0
[oct1]   [oct2]   [oct3]   [oct4]
 1–8      9–16    17–24    25–32
```

**Total IPs = 2^(32 − prefix)**

| CIDR | Fixed bits | Free bits | Octets free to change    | IP range (example)            | Total IPs  | AWS Usable |
|------|------------|-----------|--------------------------|-------------------------------|------------|------------|
| /8   | 8          | 24        | oct2, oct3, oct4 (0–255) | `10.0.0.0` → `10.255.255.255` | 16,777,216 | 16,777,211 |
| /16  | 16         | 16        | oct3, oct4 (0–255)       | `10.0.0.0` → `10.0.255.255`   | 65,536     | 65,531     |
| /24  | 24         | 8         | oct4 only (0–255)        | `10.0.0.0` → `10.0.0.255`     | 256        | 251        |
| /28  | 28         | 4         | last 4 bits of oct4      | `10.0.0.0` → `10.0.0.15`      | 16         | 11         |
| /32  | 32         | 0         | nothing — single IP      | `10.0.0.1` only               | 1          | —          |

> VPC CIDR must be between **/16** (max) and **/28** (min).

---

## AWS Reserved IPs per Subnet

AWS always reserves **5 IPs** in every subnet — you cannot assign these to resources.

For `10.0.0.0/24`:

```
10.0.0.0   → Network address
10.0.0.1   → VPC router
10.0.0.2   → AWS DNS
10.0.0.3   → AWS reserved (future use)
10.0.0.255 → Broadcast address
```

So a `/24` gives 256 − 5 = **251 usable IPs**, not 256.
