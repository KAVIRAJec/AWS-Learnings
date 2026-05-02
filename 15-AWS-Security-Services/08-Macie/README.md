## AWS Macie

AWS Macie is a **fully managed data security service** that uses machine learning to automatically discover, classify, and protect **sensitive data stored in Amazon S3** — identifies PII, financial data, credentials, and other sensitive content, then alerts you to risks.

- Scans **S3 buckets only** — not EBS, RDS, or other storage services.
- Fully managed — no infrastructure to deploy. Enable per region with one click.
- Findings sent to **Security Hub** and **EventBridge** automatically.

---

## What Macie Detects

**Sensitive Data Types:**
- **PII (Personally Identifiable Information)**: Names, addresses, phone numbers, email addresses, passport numbers, driver's licence numbers.
- **Financial data**: Credit card numbers, bank account numbers, SWIFT codes.
- **Credentials**: API keys, AWS secret keys, private keys, passwords in files.
- **Health information**: Medical record numbers, health insurance IDs (PHI).
- **Custom data identifiers**: Define your own regex patterns or keywords to detect organisation-specific sensitive data (e.g., internal employee IDs, contract numbers).

**Bucket-Level Security Findings:**
- Buckets that are **publicly accessible**.
- Buckets that are **unencrypted**.
- Buckets **shared with external AWS accounts**.
- Buckets with **replication disabled**.

---

## Key Concepts

- **Sensitive Data Discovery Job**: A scan job you create — define which S3 buckets to scan, on a schedule (daily, weekly, monthly) or one-time. Macie samples or fully scans objects based on configuration.
- **Finding**: An alert generated when sensitive data or a bucket security issue is detected.
  - **Sensitive data finding**: Macie found sensitive content inside an object (e.g., a CSV with credit card numbers).
  - **Policy finding**: A bucket configuration violates security best practices (e.g., bucket is public).
- **Managed Data Identifiers**: Pre-built detectors maintained by AWS — cover 100+ sensitive data types across multiple countries and compliance frameworks (GDPR, HIPAA, PCI DSS).
- **Custom Data Identifiers**: Your own regex + keyword rules to detect proprietary sensitive data patterns.
- **Suppression Rules**: Filter out expected findings (e.g., a known-public bucket) to reduce noise.

---

## Multi-Account Setup

- Enable via **AWS Organizations** — designate a Macie administrator account.
- **Administrator account**: Manages and views findings across all member accounts centrally.
- **Member accounts**: S3 buckets in each account are scanned — findings aggregated to the administrator.
- New accounts joining the org can be auto-enrolled in Macie.

---

## Alerts & Integrations

- Findings published to **EventBridge** — trigger Lambda for automated remediation (e.g., block public access, move sensitive objects to a private bucket).
- Findings forwarded to **Security Hub** — visible in the central security posture dashboard.
- Findings stored in an **S3 findings repository** — query with Athena for custom reporting.

---

## Macie vs GuardDuty vs Inspector

| | Macie | GuardDuty | Inspector |
|---|---|---|---|
| **Focus** | Sensitive data in S3 | Active threats across account | Vulnerabilities in workloads |
| **What it scans** | S3 object content | CloudTrail, VPC Flow Logs, DNS, EKS, RDS | EC2 packages, ECR images, Lambda code |
| **ML used for** | Classifying sensitive data | Anomaly and threat detection | CVE risk scoring |
| **Output** | Sensitive data + bucket policy findings | Threat findings | Vulnerability findings with risk score |
| **Best for** | Data privacy, compliance (GDPR, HIPAA, PCI) | Threat detection, incident response | Patch prioritisation, vulnerability management |
