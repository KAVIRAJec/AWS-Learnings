## Internet Gateway (IGW)

An **Internet Gateway** is a horizontally scaled, redundant, highly available VPC component that enables bidirectional communication between your VPC and the internet.

- **One IGW per VPC** — you attach it to the VPC, not to a subnet or instance.
- Fully managed by AWS — no bandwidth limits, no availability concerns.
- Does nothing alone — a **route table entry** pointing to the IGW is required to make a subnet public.

---

## How It Works

For an instance to be reachable from the internet, **all three** must be true:

```
1. VPC has an IGW attached
2. Subnet's route table has:  0.0.0.0/0  →  igw-xxxxxxxx
3. Instance has a public IP or Elastic IP
```

**Traffic flow (outbound):**
```
EC2 (private IP: 10.0.1.5)
  → IGW performs NAT: replaces 10.0.1.5 with the instance's public IP
  → Internet
```

**Traffic flow (inbound):**
```
Internet → IGW
  → IGW translates destination public IP → private IP 10.0.1.5
  → EC2 instance
```

The IGW maintains a **1-to-1 NAT(Network access translation) mapping** between the instance's private IP and its public/Elastic IP — it does not share IPs across instances.

---

## IGW vs NAT Gateway

| | Internet Gateway | NAT Gateway |
|---|---|---|
| **Direction** | Inbound + Outbound | Outbound only |
| **Used for** | Public subnets | Private subnets |
| **Inbound from internet** | Yes | No |
| **Attached to** | VPC | Subnet (in public subnet) |
| **Managed by** | AWS (free) | AWS (charged per hour + data) |
