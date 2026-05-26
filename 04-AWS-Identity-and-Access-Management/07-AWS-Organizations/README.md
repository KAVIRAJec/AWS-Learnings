### AWS Organizations
AWS Organizations lets you create and manage multiple AWS accounts under a single organization — centralized billing, governance, and policy enforcement across all accounts.

- **Management Account**: The account that creates the organization. Has full control. Cannot be restricted by SCPs.
- **Member Accounts**: All other accounts in the org. Can be grouped into OUs.
- **Organizational Units (OUs)**: Folders for grouping accounts — e.g., `Prod`, `Dev`, `Finance`. SCPs applied to an OU inherit down to all accounts within it.
- To remove an account from the org, it must first be made a standalone account.

---

## Service Control Policies (SCPs)

SCPs are **permission boundaries** applied at the Organization root, OU, or individual account level. They define the **maximum permissions** any IAM entity (user, role, even root user of a member account) inside that account can have.

### The Key Rule — Intersection

An action is only allowed if it is permitted by **both** the SCP and the IAM policy. If either one blocks it, the action is denied.

### Two Strategies — Allow List vs Deny List

**Deny List (default, most common):**
- AWS attaches a default `FullAWSAccess` SCP to every account/OU — everything allowed.
- You add explicit **Deny** statements to block specific actions.
- Easy to manage — only write what you want to restrict.

```json
{
  "Effect": "Deny",
  "Action": ["ec2:DeleteVpc", "s3:DeleteBucket"],
  "Resource": "*"
}
```

**Allow List (strict, zero-trust):**
- Remove the `FullAWSAccess` SCP.
- Explicitly allow only the services/actions you want.
- Anything not listed is implicitly denied — tightest control.

```json
{
  "Effect": "Allow",
  "Action": ["s3:*", "ec2:Describe*"],
  "Resource": "*"
}
```

### Inheritance — How SCPs Flow Down

SCPs stack down the hierarchy. An account receives the **intersection** of all SCPs applied at every level above it.

```
Root  (SCP: deny us-west-2 region)
  └── OU: Prod  (SCP: deny ec2:TerminateInstances)
        └── Account A
              └── IAM Role with AdministratorAccess

Account A effective permissions:
  ✗ Cannot use us-west-2  (blocked at Root)
  ✗ Cannot terminate EC2  (blocked at Prod OU)
  ✓ Everything else AdministratorAccess allows
```

If a parent OU removes an allow, child accounts lose it too — even if their own SCP allows it.

### What SCPs Can and Cannot Do

| | SCPs |
|---|---|
| Apply to | All IAM users, roles, and the member account root user |
| **Do NOT apply to** | **Management account** — SCPs never restrict it, ever |
| Grant permissions? | No — they only restrict. IAM policies still needed to grant access |
| Affect resource-based policies? | No — S3 bucket policies, KMS key policies are not restricted by SCPs |
| Affect service-linked roles? | No — service-linked roles bypass SCPs |

> SCPs do **not grant** any permissions on their own — an IAM policy must still explicitly allow the action. SCP just caps what the IAM policy can reach.
- **Tag Policies**: Enforce consistent tagging standards across accounts — define allowed tag keys and values for resources. Non-compliant resources are flagged but not blocked (unless combined with SCPs). Applied at root, OU, or account level.
- **Consolidated Billing**: All member account charges roll up to the management account — single invoice, volume discounts aggregated across accounts.
  - **RI & Savings Plans sharing**: Reserved Instance and Savings Plan discounts apply across all accounts in the org (same region, same AZ for RIs).

  - **Benefits**:
    - **VPC Sharing**: Share VPCs across accounts in the org.
    - **Cross-account access**: Use IAM roles to allow users in one account to access resources in another account.
    - **Centralized management**: Manage policies, permissions, and billing from a single account.
    - **Tags**: Use tags to organize and manage accounts, resources, and permissions.
    - **CloudWatch**: Monitor and log activity across all accounts in the org. Set up centralized S3 buckets for log storage and analysis.
    - **CloudTrail**: Track API calls across all accounts in the org for security and compliance.

---

## VPC Sharing

VPC Sharing lets multiple AWS accounts within the **same AWS Organization** launch resources into **shared subnets** from a centrally managed VPC — without needing VPC Peering or a complex network topology.

Powered by **AWS RAM (Resource Access Manager)**.

### How It Works

```
Management Account (VPC Owner)
  └── VPC: 10.0.0.0/16
        ├── Subnet A (10.0.1.0/24)  ──shared via RAM──▶  Account B (launches EC2 here)
        └── Subnet B (10.0.2.0/24)  ──shared via RAM──▶  Account C (launches RDS here)
```

- The **owner account** creates the VPC and shares specific **subnets** via RAM.
- **Participant accounts** can see the shared subnets and launch resources (EC2, RDS, Lambda ENIs, etc.) into them.
- Resources in different accounts but the same subnet communicate over **private IPs directly** — no peering, no data transfer charges between them.

### What the Owner Controls vs What Participants Control

| | Owner Account | Participant Account |
|---|---|---|
| VPC, subnets, route tables, IGW, NAT GW | Full control | Read-only (cannot modify) |
| Security Groups | Own SGs | Creates and manages **their own SGs** |
| Resources (EC2, RDS, etc.) | Can launch in own subnets | Can launch in **shared** subnets |
| Delete shared subnet | Yes | No |

> Participants cannot see or use the owner's security groups — each account manages its own SGs within the shared subnet.

### VPC Sharing vs VPC Peering

| | VPC Sharing | VPC Peering |
|---|---|---|
| **Network boundary** | Single VPC, multiple accounts | Two separate VPCs connected |
| **IP overlap** | Not possible (same VPC CIDR) | CIDRs must not overlap |
| **Requires AWS Org** | Yes (same org) | No |
| **Transitive routing** | N/A — same network | Not supported |
| **Best for** | Multi-account, single shared network | Connecting separate VPCs |
