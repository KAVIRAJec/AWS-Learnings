### AWS Control Tower

AWS Control Tower automates the setup of a **secure, well-architected multi-account AWS environment** (called a Landing Zone) based on AWS best practices — orchestrates Organizations, SCPs, IAM Identity Center, CloudTrail, and AWS Config under the hood.

- Built on top of AWS Organizations — Control Tower is the opinionated, automated layer on top of it.
- Suited for organizations that want a governed multi-account setup without manually wiring every service together.

**Key Concepts:**
- **Landing Zone**: The pre-configured, multi-account AWS environment Control Tower sets up — includes the account structure, guardrails, logging, and SSO out of the box.
- **Account Factory**: Automated account provisioning via AWS Service Catalog — create new AWS accounts with pre-applied baselines (guardrails, networking, logging) from a self-service portal. Supports account customization using **Account Factory for Terraform (AFT)**.
- **Guardrails (Controls)**: Pre-packaged governance rules applied across accounts. Two types:
  - **Preventive**: Implemented as **SCPs** — block non-compliant actions entirely (e.g., deny disabling CloudTrail, cross region access).
  - **Detective**: Implemented as **AWS Config rules** — detect and flag non-compliant resources (e.g., flag EC2 instances without required tags).
  - **Proactive**: Implemented as **CloudFormation hooks** — check resources for compliance before they are provisioned.
  - Guardrail enforcement levels: **Mandatory** (always on, cannot disable), **Strongly Recommended**, **Elective** (opt-in).
- **Control Tower Dashboard**: Central console showing compliance status of all enrolled accounts and OUs — which guardrails are active, which accounts are non-compliant, and overall governance health.

**Reserved Accounts (created automatically):**
- **Management Account**: Owns the organization. Used only for billing and governance — workloads should not run here.
- **Log Archive Account**: Centralized repository for CloudTrail logs and AWS Config snapshots from all accounts — read-only access enforced by guardrails.
- **Audit Account**: Provides read-only cross-account access for security and compliance reviews — used by auditors and security tools.

**Control Tower vs Manual Organizations Setup:**

| | AWS Control Tower | Manual AWS Organizations |
|---|---|---|
| **Setup** | Automated — Landing Zone in hours | Manual — wire each service yourself |
| **Guardrails** | Pre-built SCPs + Config rules | Write all SCPs and Config rules from scratch |
| **Account provisioning** | Account Factory — self-service, baseline applied automatically | Manual account creation, no baseline enforcement |
| **Logging** | Auto-configured CloudTrail + Config to Log Archive account | Manual setup per account |
| **SSO** | IAM Identity Center configured automatically | Manual IAM Identity Center setup |
| **Best for** | New orgs or orgs wanting a governed baseline fast | Orgs needing full custom control over every detail |
