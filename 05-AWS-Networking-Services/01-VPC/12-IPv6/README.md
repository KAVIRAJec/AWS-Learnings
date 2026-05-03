## IPv6 in AWS VPC

IPv4 addresses are 32-bit (4.3 billion total) — they are running out. IPv6 is 128-bit, providing virtually unlimited addresses (`340 undecillion` — 3.4 × 10³⁸).

AWS VPC supports **dual-stack** — a VPC can run IPv4 and IPv6 simultaneously. IPv4 is always required; IPv6 is optional and added on top.

---

## IPv4 vs IPv6

| | IPv4 | IPv6 |
|---|---|---|
| **Bits** | 32-bit | 128-bit |
| **Format** | `10.0.1.5` (decimal, 4 octets) | `2001:db8::1a2b` (hex, 8 groups of 16 bits) |
| **Total addresses** | ~4.3 billion | 340 undecillion |
| **Private ranges** | Yes (`10.x`, `172.16.x`, `192.168.x`) | No private ranges — all are globally routable |
| **NAT needed** | Yes (private IPs need NAT to reach internet) | No — every IPv6 address is public |
| **AWS VPC** | Always required | Optional (dual-stack) |

---

## How IPv6 Works in VPC

**Step 1 — Assign IPv6 CIDR to VPC:**
- AWS assigns a `/56` IPv6 CIDR to your VPC from Amazon's pool (e.g. `2600:1f18:abcd::/56`).
- You cannot choose the range (unlike IPv4) — AWS picks it.

**Step 2 — Assign IPv6 CIDR to Subnet:**
- Each subnet gets a `/64` block carved from the VPC's `/56`.
- A `/64` gives `2^64` addresses per subnet — essentially unlimited.

**Step 3 — Assign IPv6 to Instances:**
- EC2 instances get an IPv6 address automatically (if subnet is configured to auto-assign).
- IPv6 addresses are **globally routable** — no NAT, no public/private distinction.

```
VPC:    2600:1f18:abcd::/56
          │
          ├── Subnet-A:  2600:1f18:abcd:0100::/64  (public)
          └── Subnet-B:  2600:1f18:abcd:0200::/64  (private — uses EIGW)
```

---

## Routing IPv6 Traffic

**Public subnet (inbound + outbound internet):**
```
Destination          Target
::/0              →  igw-xxxxxxxx    ← all IPv6 traffic → Internet Gateway
10.0.0.0/16       →  local
2600:1f18::/56    →  local
```

**Private subnet (outbound only):**
```
Destination          Target
::/0              →  eigw-xxxxxxxx   ← all IPv6 traffic → Egress-Only IGW
10.0.0.0/16       →  local
2600:1f18::/56    →  local
```

> `::/0` is the IPv6 equivalent of `0.0.0.0/0` — matches all IPv6 addresses.

---

## Egress-Only Internet Gateway (EIGW)

Since IPv6 addresses are all public (no NAT), a private IPv6 instance would be reachable from the internet if connected to a regular IGW. The **Egress-Only Internet Gateway** solves this — it allows **outbound IPv6 traffic only**, blocking all inbound connections from the internet.

- IPv6 equivalent of a **NAT Gateway** (outbound-only).
- Stateful — return traffic for outbound connections is allowed back in.
- **Free** — no hourly charge (unlike NAT Gateway).
- Only works with IPv6 — does not handle IPv4.

```
Without EIGW (bad — instance reachable from internet):
Private EC2 (IPv6) ──► IGW ──► Internet
Internet           ──► IGW ──► Private EC2  ← inbound allowed ✗

With EIGW (correct — outbound only):
Private EC2 (IPv6) ──► EIGW ──► Internet   ← outbound allowed ✓
Internet           ──► EIGW ──► BLOCKED     ← inbound blocked ✓
```

---

## NAT Gateway vs Egress-Only Internet Gateway

| | NAT Gateway | Egress-Only IGW |
|---|---|---|
| **IP version** | IPv4 | IPv6 |
| **Direction** | Outbound only | Outbound only |
| **Inbound blocked** | Yes | Yes |
| **Cost** | Per hour + per GB | Free |
| **Stateful** | Yes | Yes |
| **Attached to** | Public subnet | VPC |
