## AWS PrivateLink

AWS PrivateLink provides **private connectivity between VPCs, AWS services, and on-premises applications** — traffic never touches the public internet, staying entirely within the AWS network.

**Core use case**: Share a service running in one account/VPC with consumers in other accounts/VPCs — without VPC Peering, internet gateways, NAT, or exposing public IPs.

---

### How It Works — NLB + ENI Pattern

PrivateLink works by pairing a **Network Load Balancer (NLB)** on the provider side with an **Elastic Network Interface (ENI)** on the consumer side:

```
Provider Account (service owner)        Consumer Account (service user)
────────────────────────────────        ────────────────────────────────
Application / Service
        │
       NLB  (fronts the service)        Interface Endpoint (ENI in consumer VPC)
        │                                        │
        └──────── PrivateLink ──────────────────►│
                 (private, within AWS network)   │
                                          Consumer App
                                   (accesses service via ENI private IP)
```

1. **Provider** creates a **VPC Endpoint Service** backed by an NLB.
2. **Consumer** creates an **Interface VPC Endpoint** (ENI) in their VPC pointing to the provider's endpoint service.
3. Traffic flows privately from consumer ENI → PrivateLink → provider NLB → application — **never leaves the AWS network**.

**Key properties:**
- No VPC Peering needed — VPCs don't need to know about each other's CIDR ranges.
- No overlapping IP concerns — consumer accesses the service via the ENI's private IP, not the provider's VPC CIDR.
- Works **cross-account and cross-region** (cross-region with additional latency/cost).
- Provider can **accept or reject** connection requests from consumers — full access control.
- Traffic is **unidirectional** — consumer initiates, provider responds. Provider cannot initiate back to consumer.

---

### Types of PrivateLink Endpoints

| Type | What it connects | Example |
|---|---|---|
| **Interface Endpoint** | AWS services or your own VPC Endpoint Service | Connect to S3, SNS, SSM, or a partner SaaS |
| **Gateway Endpoint** | S3 and DynamoDB only — free, via route table | S3 access from private subnet without internet |

> **Gateway Endpoints** are free and use route table entries (not ENIs). **Interface Endpoints** create ENIs in your subnet and have an hourly cost.

---

### PrivateLink vs VPC Peering vs Transit Gateway

| | PrivateLink | VPC Peering | Transit Gateway |
|---|---|---|---|
| **Traffic scope** | Specific service only (not whole VPC) | Full VPC-to-VPC routing | Full network routing hub |
| **No VPC Peering needed** | Yes | N/A | Yes |
| **Overlapping CIDRs** | Supported | Not supported | Not supported |
| **Direction** | One-way (consumer → provider) | Bidirectional | Bidirectional |
| **Cross-account** | Yes | Yes | Yes |
| **Cost** | Per endpoint-hour + data transfer | Free (data transfer cost only) | Per attachment + data transfer |
| **Best for** | Securely expose a specific service to other accounts | Simple VPC-to-VPC full access | Large multi-VPC hub-and-spoke |