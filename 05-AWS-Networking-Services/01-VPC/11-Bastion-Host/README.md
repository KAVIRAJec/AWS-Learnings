## Bastion Host

A **Bastion Host** is an EC2 instance placed in a **public subnet** that acts as a secure jump server — you SSH into the bastion first, then from there SSH into instances in private subnets.

- Private subnet instances have no public IP and no route to IGW — they are unreachable directly from the internet.
- The bastion is the **only entry point** into the private network, so it is hardened and tightly controlled.
- Also called a **Jump Server** or **Jump Box**.

---

## Architecture

```
Your Machine (laptop)
        │
        │  SSH (port 22)
        ▼
┌───────────────────┐
│   Public Subnet   │
│  ┌─────────────┐  │
│  │Bastion Host │  │  ← Has public IP, IGW route, strict SG (your IP only)
│  └─────────────┘  │
└────────┬──────────┘
         │  SSH (port 22)
         ▼
┌───────────────────┐
│   Private Subnet  │
│  ┌─────────────┐  │
│  │  EC2 / RDS  │  │  ← No public IP, no IGW route
│  └─────────────┘  │
└───────────────────┘
```

---

## Route Tables

**Public subnet (Bastion Host):**
```
Destination     Target
10.0.0.0/16  →  local          ← VPC-internal traffic (bastion → private EC2)
0.0.0.0/0    →  igw-xxxxxxxx   ← internet access (your laptop → bastion)
```

**Private subnet (target EC2):**
```
Destination     Target
10.0.0.0/16  →  local          ← VPC-internal traffic (receives SSH from bastion)
0.0.0.0/0    →  nat-xxxxxxxx   ← outbound-only internet (e.g. package updates) — optional
```

> The private subnet has **no IGW route** — this is what makes it unreachable from the internet directly. The bastion reaches it via the `local` route (both are inside the same VPC CIDR).

---

## Security Best Practices

- **Lock down the Bastion SG** — allow SSH (port 22) only from your specific IP (`x.x.x.x/32`), not `0.0.0.0/0`.
- **Private instance SG** — allow SSH only from the Bastion's security group (not from the internet).
- Use **SSH Agent Forwarding** (`ssh -A`) — so your private key never leaves your machine (never copy the key onto the bastion).
- Shut down the bastion when not in use to reduce attack surface.
- Consider **AWS Systems Manager Session Manager** as a keyless, agentless alternative — no port 22 needed at all.

---

## Bastion Host vs SSM Session Manager

| | Bastion Host | SSM Session Manager |
|---|---|---|
| **Access method** | SSH over port 22 | HTTPS via SSM Agent — no open ports |
| **Public IP needed** | Yes (on bastion) | No |
| **Key management** | SSH key pairs required | IAM-based, no keys |
| **Audit trail** | Manual (SSH logs) | Full session logs in CloudTrail + S3 |
| **Cost** | EC2 instance cost | Free (SSM is free) |
| **Best for** | Simple setups, legacy access | Secure, auditable, keyless access |
