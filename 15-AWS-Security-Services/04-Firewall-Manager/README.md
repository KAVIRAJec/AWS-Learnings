## AWS Firewall Manager

AWS Firewall Manager is a **centralized security management service** that lets you configure and enforce firewall rules across multiple AWS accounts and resources in an AWS Organization — from a single place, once, automatically applied everywhere.

- Requires **AWS Organizations** — Firewall Manager operates at the org level.
- Must be enabled in the **Firewall Manager administrator account** (management account or a delegated admin account).
- New accounts joining the org automatically get policies applied — no manual setup per account.

---

## Key Concepts

- **Security Policy**: The core resource — defines what rules to enforce and which accounts/resources to apply them to. Firewall Manager automatically creates and maintains the underlying resources (WAF Web ACLs, Shield protections, security groups) in each in-scope account.
- **Policy Scope**: Define which accounts and resources the policy applies to — all accounts, specific OUs, specific tags, or exclude certain accounts.
- **Remediation**: Policies can be set to **auto-remediate** — if a resource is found non-compliant (e.g., a new ALB without a WAF Web ACL), Firewall Manager automatically attaches the correct rules.

---

## What Firewall Manager Can Manage

- **AWS WAF rules** — enforce a common Web ACL across all CloudFront distributions, ALBs, and API Gateways in the org.
- **AWS Shield Advanced** — enroll all accounts in Shield Advanced and ensure all critical resources are protected.
- **VPC Security Groups** — audit and enforce security group rules across EC2 instances and ENIs (e.g., ensure no security group allows unrestricted `0.0.0.0/0` inbound on port 22).
- **AWS Network Firewall** — deploy and manage Network Firewall policies across VPCs in multiple accounts.
- **Route 53 Resolver DNS Firewall** — enforce DNS-level filtering rules across accounts.

---

## How It Works

```
AWS Organizations
│
└─► Firewall Manager Administrator Account
          │
          │  Define Security Policy
          │  (WAF / Shield / Security Group / Network Firewall)
          │
          ├─► Account A  → Policy auto-applied → Resources protected
          ├─► Account B  → Policy auto-applied → Resources protected
          ├─► Account C  → Policy auto-applied → Resources protected
          └─► New Account joins org → Policy auto-applied immediately
```

- Non-compliant resources are flagged in the **Firewall Manager console** with compliance status per account and resource.
- Auto-remediation creates the missing protection automatically — no manual intervention per account.

---

## Firewall Manager vs Manual WAF / Shield Setup

| | Firewall Manager | Manual per-account setup |
|---|---|---|
| **Scope** | Entire AWS Organization | One account at a time |
| **New accounts** | Auto-covered on join | Manual setup required |
| **Consistency** | Enforced — deviations flagged and auto-fixed | Prone to drift |
| **Compliance visibility** | Centralized dashboard | Check each account separately |
| **Cost** | $100/policy/region/month | No extra cost (but operational overhead) |
| **Best for** | Orgs with many accounts needing uniform security | Single account or small setups |

---

## Firewall Manager vs WAF vs Shield

| | WAF | Shield | Firewall Manager |
|---|---|---|---|
| **What it does** | Inspects and filters HTTP traffic | Detects and mitigates DDoS attacks | Centrally manages WAF + Shield + SGs across org |
| **Scope** | Single resource | Single resource (Advanced) or all (Standard) | Entire AWS Organization |
| **Requires Organizations** | No | No | Yes |
| **Best for** | App-layer protection | DDoS resilience | Multi-account governance and enforcement |
