## AWS Config

AWS Config continuously monitors and records the **configuration state** of your AWS resources and evaluates them against desired rules.

**Key Concepts:**
- **Configuration Item**: A snapshot of a resource's configuration at a point in time.
- **Configuration History**: Full history of configuration changes for a resource — useful for auditing.
- **Config Rules**: Evaluate whether resources comply with your desired settings (e.g., "S3 buckets must have versioning enabled"). Can be AWS-managed or custom (Lambda-backed).
- **Conformance Packs**: A collection of Config rules and remediation actions packaged together for compliance frameworks (e.g., CIS, PCI-DSS).
- **Remediation**: Automatically fix non-compliant resources using **SSM Automation** documents.
- Data stored in **S3**; can stream to **SNS** for notifications.

**Use cases:** Compliance auditing, change management, security analysis, resource inventory.
