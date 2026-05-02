## AWS Shield

AWS Shield is a **managed DDoS (Distributed Denial of Service) protection service** that safeguards AWS applications against network and transport layer attacks — automatically detects and mitigates volumetric, protocol, and application-layer attacks.

- Two tiers: **Standard** (free, automatic) and **Advanced** (paid, opt-in).

---

## Shield Standard

- **Automatically enabled** for all AWS customers at no extra cost — no setup required.
- Protects against the most common and frequent **Layer 3 and Layer 4** DDoS attacks:
  - **Volumetric attacks**: UDP floods, ICMP floods, reflection attacks — overwhelm bandwidth.
  - **Protocol attacks**: SYN floods, Ping of Death — exhaust connection state tables.
- Applies to all AWS resources by default: EC2, ELB, CloudFront, Route 53, Global Accelerator.
- No visibility into attacks — you won't see attack details or metrics.

---

## Shield Advanced

- **Paid tier** — $3,000/month per organization + data transfer fees.
- Includes everything in Standard, plus:

**Enhanced Protection:**
- Protects against **Layer 3, 4, and 7** attacks — including large, sophisticated, and prolonged DDoS campaigns.
- Protects specific resources: **EC2, ELB (ALB/NLB/CLB), CloudFront, Route 53, Global Accelerator**.
- Near real-time visibility into attacks — metrics, attack summaries, and forensics in the console.

**DDoS Response Team (DRT):**
- 24/7 access to **AWS DDoS Response Team** — they can write custom WAF rules, tune mitigations, and assist during an active attack.
- Proactive engagement — DRT can contact you if they detect an attack on your resources before you notice.

**Cost Protection:**
- **DDoS cost protection** — AWS credits any EC2, ELB, CloudFront, or Route 53 scaling costs incurred due to a DDoS attack. Prevents bill shock from attack-induced traffic spikes.

**WAF Integration:**
- Shield Advanced integrates with **AWS WAF** for Layer 7 protection — DRT can create and deploy WAF rules automatically during an attack.
- Automatic application layer (Layer 7) DDoS mitigation when Shield Advanced detects an anomaly.

**Alarms & Visibility:**
- CloudWatch metrics: `DDoSDetected`, `DDoSAttackBitsPerSecond`, `DDoSAttackPacketsPerSecond`, `DDoSAttackRequestsPerSecond`.
- Set CloudWatch alarms to notify via SNS when an attack is detected.

---

## Shield Standard vs Advanced

| | Shield Standard | Shield Advanced |
|---|---|---|
| **Cost** | Free | $3,000/month per org |
| **Protection layers** | Layer 3 / 4 | Layer 3 / 4 / 7 |
| **Attack visibility** | None | Real-time metrics + attack reports |
| **DRT access** | No | Yes — 24/7 |
| **WAF integration** | No | Yes — automatic Layer 7 mitigation |
| **Cost protection** | No | Yes — credits for DDoS-induced scaling |
| **Proactive engagement** | No | Yes |
| **Best for** | Basic protection for all workloads | Critical, internet-facing production apps |

---

## Shield vs WAF

| | AWS Shield | AWS WAF |
|---|---|---|
| **Protects against** | DDoS — volumetric, protocol, application floods | Web exploits — SQLi, XSS, bot abuse, rate limiting |
| **Layer** | 3 / 4 (Standard), 3 / 4 / 7 (Advanced) | 7 only |
| **Traffic inspection** | Volume and rate patterns | HTTP request content (headers, body, URI) |
| **Best used together** | Shield Advanced + WAF for complete protection | — |
