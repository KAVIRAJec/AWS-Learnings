## AWS Security Hub

AWS Security Hub is a **centralized security findings aggregator and compliance dashboard** — collects, normalizes, and prioritizes security findings from multiple AWS services and third-party tools into a single place.

- Security Hub itself does **not detect threats** — it aggregates findings from services that do (GuardDuty, Inspector, Macie, Config, Firewall Manager, etc.).
- Must be **enabled per region** — findings are regional. Use the aggregation feature to view all regions from one.

---

## Key Concepts

- **Finding**: A normalized security alert ingested from a source service — standardized into **ASFF (Amazon Security Finding Format)** regardless of the source.
- **Insight**: A pre-built or custom query that groups and filters findings — e.g., "Top EC2 instances by critical finding count", "All findings from the last 7 days with HIGH severity".
- **Controls**: Individual security checks mapped to compliance standards — each control has a status: `PASSED`, `FAILED`, `NOT AVAILABLE`.
- **Security Score**: An overall percentage score (0–100%) per standard showing how many controls are passing across your accounts.

---

## Finding Sources

Security Hub ingests findings from:

**AWS Native:**
- **GuardDuty** — threat detection findings
- **Inspector** — vulnerability findings (EC2, ECR, Lambda)
- **Macie** — sensitive data discovery findings (S3)
- **AWS Config** — compliance rule violations
- **Firewall Manager** — policy compliance findings
- **IAM Access Analyzer** — unintended external resource access findings
- **Systems Manager Patch Manager** — patch compliance findings

**Third-Party (via integrations):**
- Splunk, Palo Alto, CrowdStrike, Tenable, and others via the Security Hub partner integrations marketplace.

**Custom:**
- Your own applications can send findings directly using the **BatchImportFindings** API in ASFF format.

---

## Compliance Standards

Security Hub maps findings and controls to industry compliance frameworks:

- **AWS Foundational Security Best Practices (FSBP)** — AWS-curated baseline controls.
- **CIS AWS Foundations Benchmark** (v1.2, v1.4) — Center for Internet Security hardening checks.
- **PCI DSS** — Payment Card Industry Data Security Standard.
- **NIST SP 800-53** — US government security controls.
- **SOC 2** — Service Organization Controls.

Each standard runs automated Config-based checks against your resources and shows pass/fail per control.

---

## Multi-Account & Multi-Region

- **Administrator account**: Designated account (via AWS Organizations or manual invite) — sees all findings from all member accounts in one consolidated view.
- **Member accounts**: Send their findings up to the administrator — cannot see findings from other members.
- **Cross-region aggregation**: Designate one region as the **aggregation region** — findings from all other regions replicate there for a single global view.

---

## Alerts & Automation

- All findings are published to **EventBridge** — trigger Lambda for automated remediation, SNS for notifications, or ticketing systems.
- **Custom Actions**: Define manual response actions in Security Hub (e.g., "Send to Jira", "Isolate EC2") — triggered from the console on selected findings, published to EventBridge.
- Findings can be **suppressed** (archived) or have their workflow status updated (`NEW` → `NOTIFIED` → `RESOLVED` → `SUPPRESSED`).

---

## Security Hub vs GuardDuty vs Inspector

| | Security Hub | GuardDuty | Inspector |
|---|---|---|---|
| **Role** | Aggregator + compliance dashboard | Threat detector | Vulnerability scanner |
| **Detects threats** | No — aggregates findings | Yes | No — finds vulnerabilities |
| **Input** | Findings from other services | CloudTrail, VPC Flow Logs, DNS, etc. | EC2 packages, ECR images, Lambda code |
| **Compliance checks** | Yes — CIS, PCI, FSBP, NIST | No | No |
| **Best used** | Central security posture view | Active threat monitoring | Vulnerability management |
