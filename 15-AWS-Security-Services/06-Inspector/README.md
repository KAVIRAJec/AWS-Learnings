## AWS Inspector

AWS Inspector is an **automated vulnerability management service** that continuously scans AWS workloads for software vulnerabilities and unintended network exposure — prioritizes findings by risk score so you know what to fix first.

- No agents needed for **Lambda and ECR** scanning. EC2 scanning requires the **SSM Agent**.
- Runs continuously — not a one-time scan. New findings are generated as new CVEs are published or workloads change.
- Findings are sent to **AWS Security Hub** and **Amazon EventBridge** automatically.

---

## What Inspector Scans

- **EC2 Instances**: OS and application package vulnerabilities — scans installed packages against the CVE database. Also checks for unintended network reachability (e.g., port open to the internet via security group).
- **ECR Container Images**: Scans container images pushed to ECR for OS and programming language package vulnerabilities — results available before deployment.
- **Lambda Functions**: Scans Lambda function code and dependencies for known vulnerabilities in application packages (Python, Node.js, Java, etc.).

---

## Findings

- Each finding includes a **risk score** (0–10) — calculated based on CVE severity + network reachability + exploitability. Higher score = fix first.
- **CVE (Common Vulnerabilities and Exposures)**: Industry-standard vulnerability identifiers (e.g., `CVE-2021-44228` — Log4Shell).
- Finding types:
  - **Package vulnerability**: A known CVE in an installed OS or application package.
  - **Network reachability**: An EC2 instance is reachable from the internet on a port it shouldn't be (based on security groups, NACLs, route tables).
  - **Code vulnerability** (Lambda): Security issues detected in Lambda function code.
- Findings routed to **Security Hub** for centralized view and **EventBridge** for automated remediation.

---

## Multi-Account Setup

- Enable via **AWS Organizations** — delegate Inspector administration to a single account.
- **Administrator account**: Centralized view of findings across all member accounts.
- **Member accounts**: Scanning enabled automatically on join — cannot be disabled by member if org-enabled.

---

## Inspector vs GuardDuty vs Config

| | Inspector | GuardDuty | AWS Config |
|---|---|---|---|
| **Purpose** | Find vulnerabilities in workloads | Detect active threats and attacks | Track configuration compliance |
| **What it checks** | CVEs, network exposure, code issues | Suspicious behavior, anomalous activity | Resource configuration against rules |
| **When** | Continuously as CVEs are published | Continuously in real time | On configuration change or schedule |
| **Output** | Vulnerability findings with risk score | Security findings (threat alerts) | Compliance status (compliant / non-compliant) |
| **Requires agent** | SSM Agent (EC2 only) | No | No |
| **Best for** | Vulnerability management, patching priority | Threat detection, incident response | Compliance auditing, drift detection |
