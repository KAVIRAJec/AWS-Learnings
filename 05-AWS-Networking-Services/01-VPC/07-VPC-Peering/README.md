## VPC Peering

A **VPC Peering connection** is a private, direct network link between two VPCs — traffic routes through AWS's internal network, never touching the internet.

- Works across **same account**, **different accounts**, and **different regions** (inter-region peering).
- Peered VPCs **must not have overlapping CIDR blocks** — if they do, the connection cannot be established.
- VPC Peering is **not transitive** — if VPC-A peers with VPC-B and VPC-B peers with VPC-C, VPC-A cannot reach VPC-C through VPC-B. Each pair needs its own peering connection.

---

## How It Works

Peering alone does nothing — you must do **3 things** for traffic to actually flow:

```
Step 1: Create the peering connection (requester → accepter)
Step 2: Accept the peering request (accepter VPC owner)
Step 3: Update route tables in BOTH VPCs
Step 4: Update security groups to allow traffic from the peered VPC
```

---

## Route Table Update (mandatory)

After peering is established, each VPC's route table must have a route pointing to the other VPC's CIDR via the peering connection:

**VPC-A route table** (CIDR: `10.0.0.0/16`):
```
Destination     Target
10.0.0.0/16  →  local
172.16.0.0/16 →  pcx-xxxxxxxx   ← route to VPC-B via peering connection
```

**VPC-B route table** (CIDR: `172.16.0.0/16`):
```
Destination     Target
172.16.0.0/16 →  local
10.0.0.0/16  →  pcx-xxxxxxxx   ← route to VPC-A via peering connection
```

> Both sides must be updated — updating only one side means traffic flows one-way only.

---

## Referencing Security Groups Across Peered VPCs

Within the **same region**, you can reference a security group from the peered VPC as the source/destination in a rule — instead of using a CIDR block.

**Example:** Allow EC2 in VPC-B to connect to RDS in VPC-A on port 3306:

```
VPC-A  RDS Security Group — Inbound rule:
  Type     : MySQL/Aurora (3306)
  Source   : sg-xxxxxxxx (VPC-B's EC2 security group ID)   ← SG reference
```

This is more precise than allowing the entire VPC-B CIDR — only instances in that specific SG can connect, not all of VPC-B.

> Cross-region peering does **not** support SG referencing — use CIDR blocks instead.

---

## Non-Transitive Peering

```
VPC-A ◄──pcx──► VPC-B ◄──pcx──► VPC-C

VPC-A cannot reach VPC-C — peering is NOT transitive.
Each pair needs its own direct peering connection.

To connect all three:
VPC-A ◄──pcx──► VPC-B
VPC-A ◄──pcx──► VPC-C
VPC-B ◄──pcx──► VPC-C
```

For many VPCs, use **AWS Transit Gateway** instead — it acts as a hub and supports transitive routing without N×(N-1)/2 peering connections.

---

## Key Constraints

| Constraint | Detail |
|---|---|
| **Overlapping CIDRs** | Not allowed — connection will be rejected |
| **Transitive routing** | Not supported |
| **Same-region SG reference** | Supported |
| **Cross-region SG reference** | Not supported — use CIDR |
| **DNS resolution** | Must enable "DNS resolution from accepter/requester" setting to use private DNS names across peered VPCs |
