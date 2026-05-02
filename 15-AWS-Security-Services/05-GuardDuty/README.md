## AWS GuardDuty

AWS GuardDuty is an **ML based intelligent threat detection service** that continuously monitors your AWS accounts and workloads for malicious activity and unauthorized behaviour — uses machine learning, anomaly detection, and integrated threat intelligence to identify threats.

- **No agents to install, no infrastructure to manage** — fully managed, enabled with one click.
- Analyzes data sources continuously in the background — minimal performance impact.
- Generates **findings** (security alerts) when suspicious activity is detected.

---

## Data Sources

GuardDuty analyzes the following sources automatically:

- **CloudTrail Events**: Monitors API calls — detects unusual IAM activity, unauthorized access attempts, suspicious console logins.
- **CloudTrail S3 Data Events**: Monitors S3 object-level activity — detects unusual access patterns, data exfiltration from S3.
- **VPC Flow Logs**: Monitors network traffic — detects communication with known malicious IPs, port scanning, unusual traffic patterns.
- **DNS Logs**: Monitors DNS queries from EC2 instances — detects instances communicating with known malicious domains (C2 servers).
- **EKS Audit Logs**: Monitors Kubernetes API activity — detects suspicious container activity, privilege escalation in EKS clusters.
- **RDS Login Activity**: Monitors RDS and Aurora login attempts — detects brute-force attacks and suspicious login patterns.
- **Lambda Network Activity**: Monitors Lambda function network behaviour — detects crypto-mining or unusual outbound calls from functions.
- **EBS Malware Scanning**: Scans EBS volumes attached to EC2 instances for malware on finding creation.

---

## Findings

A **finding** is a security alert generated when GuardDuty detects a threat. Each finding includes:
- **Severity**: Low, Medium, High — prioritize response accordingly.
- **Finding type**: Categorized by threat (e.g., `UnauthorizedAccess:EC2/SSHBruteForce`, `CryptoCurrency:EC2/BitcoinTool`, `Trojan:EC2/BlackholeTraffic`).
- **Affected resource**: Which EC2, S3 bucket, IAM user, or Lambda was involved.
- **Evidence**: IP addresses, API calls, DNS queries that triggered the finding.

**Finding categories:**
- **CryptoCurrency** — instance or Lambda running crypto-mining software.
- **Backdoor** — instance communicating with known C2 server.
- **Pentest** — activity resembling penetration testing tools (Kali Linux, Parrot).
- **Trojan** — malware-related network activity.
- **UnauthorizedAccess** — SSH/RDP brute force, unusual API calls from known bad IPs.
- **Recon** — port scanning, unusual describe/list API calls suggesting reconnaissance.
- **Stealth** — CloudTrail disabled, password policy weakened, unusual log deletion.
- **CredentialAccess** — suspicious credential usage, IAM key exfiltration attempts.

---

## Multi-Account Setup

- **Administrator account**: Manages GuardDuty across the org — sees all findings from all member accounts in one place.
- **Member accounts**: Individual accounts in the org — findings are aggregated to the administrator account.
- Enable via **AWS Organizations** — one-click enablement across all current and future accounts.
- Member accounts cannot disable GuardDuty if it was enabled by the administrator.

---

## Alerts & Integrations

- Findings are published to **Amazon EventBridge** — trigger Lambda, SNS notifications, or automated remediation workflows.
- Findings also appear in **AWS Security Hub** — centralized security findings dashboard.
- **Suppression rules**: Suppress known-safe findings (e.g., internal security scanners) to reduce noise — suppressed findings are still logged but not alerted.
- **Trusted IP list**: Whitelist IP addresses that should never trigger findings.
- **Threat IP list**: Custom list of known malicious IPs to add on top of AWS threat intelligence.

---

## GuardDuty vs CloudTrail vs Security Hub

| | GuardDuty | CloudTrail | Security Hub |
|---|---|---|---|
| **Purpose** | Threat detection — find active attacks | Audit log — record all API activity | Aggregation — centralize findings from all security tools |
| **Output** | Security findings (alerts) | Raw API call logs | Normalized findings from GuardDuty, Inspector, Config, etc. |
| **ML/Intelligence** | Yes — anomaly detection + threat intel | No | No — aggregates, doesn't detect |
| **Action on finding** | EventBridge → Lambda / SNS | CloudWatch Logs / S3 | EventBridge → Lambda / SNS |
| **Best for** | Detecting active threats in real time | Forensics, compliance, change history | Unified security posture view |
