## AWS Config

AWS Config continuously monitors and records the **configuration state** of your AWS resources and evaluates them against desired compliance rules — answers "what did this resource look like at any point in time, and is it compliant right now?"

- Regional service — must be enabled per region. Can aggregate results across regions and accounts.
- Stores configuration history and snapshots in an **S3 bucket**. Can stream configuration change notifications to **SNS**.

---

## Core Concepts

**Key Concepts:**
- **Configuration Item (CI)**: A point-in-time snapshot of a resource's full configuration — attributes, relationships, tags, IAM permissions, and metadata. Created whenever a resource changes.
- **Configuration History**: The full timeline of CIs for a resource — lets you see exactly what changed, when, and what it looked like before.
- **Configuration Snapshot**: A point-in-time dump of all CIs for all tracked resources in a region — delivered to S3 on demand or on a schedule.
- **Configuration Recorder**: The component that detects changes and records CIs. Must be started to begin recording. Records all supported resource types by default, or a specific subset.
- **Delivery Channel**: Defines where Config delivers snapshots and notifications — S3 bucket (required) and SNS topic (optional).
- **Resource Relationships**: Config maps how resources relate to each other (e.g., which EC2 instance uses which security group, which subnet it's in) — useful for impact analysis.

---

## Config Rules

Config Rules define the **desired compliance state** for your resources. Config evaluates each resource against its rules and marks it as `COMPLIANT` or `NON_COMPLIANT`.

**Rule Types:**
- **AWS Managed Rules**: Pre-built rules provided by AWS — 200+ available. Examples:
  - `s3-bucket-versioning-enabled` — S3 buckets must have versioning on.
  - `ec2-instances-in-vpc` — EC2 instances must be inside a VPC.
  - `encrypted-volumes` — EBS volumes must be encrypted.
  - `iam-password-policy` — IAM account password policy must meet requirements.
- **Custom Rules**: Your own logic backed by a **Lambda function** — evaluate any condition you can code. Triggered on configuration changes or periodically.
- **Service-Linked Rules**: Rules managed by other AWS services (e.g., Security Hub) that appear in Config.

**Evaluation Triggers:**
- **Configuration Change**: Rule runs whenever a matching resource is created, modified, or deleted.
- **Periodic**: Rule runs on a schedule (1 hour, 3 hours, 6 hours, 12 hours, 24 hours) — useful for rules that check external state.

---

## Remediation

When a resource is found `NON_COMPLIANT`, Config can automatically or manually trigger remediation.

- **Automatic Remediation**: Runs an **SSM Automation document** when a non-compliant resource is detected — no human intervention needed.
- **Manual Remediation**: Marks the resource non-compliant and lets you trigger the fix on demand from the console or API.
- **Retry on failure**: Configure the number of retry attempts if the remediation action fails.
- **Examples**:
  - Non-compliant S3 bucket (public access on) → SSM automation enables block public access.
  - EC2 instance without required tags → Lambda-backed rule + SSM automation applies default tags.
  - Unencrypted EBS volume → automation creates an encrypted snapshot and replaces the volume.

---

## Conformance Packs

A **Conformance Pack** is a collection of Config rules and optional remediation actions bundled together to represent a compliance framework.

- Deploy a full compliance framework (e.g., CIS AWS Foundations, PCI-DSS, HIPAA, NIST) as a single unit — instead of adding rules one by one.
- AWS provides **sample conformance packs** for common frameworks; you can also author custom ones using YAML templates.
- Deployed via **CloudFormation** — can be applied to a single account/region or across an entire AWS Organization.
- Compliance status is aggregated at the pack level — see overall pack compliance alongside individual rule results.

---

## Multi-Account and Multi-Region Aggregation

- **Aggregator**: Collects Config data (resource inventory, compliance results) from multiple accounts and regions into a single view.
- Source accounts can be individual accounts or all accounts in an **AWS Organization**.
- Does not deploy rules to source accounts — it only reads and aggregates results. Rules must be deployed separately in each account/region.
- Use case: Central compliance dashboard for an entire organization without logging into each account.

---

## AWS Config vs CloudTrail

| | AWS Config | CloudTrail |
|---|---|---|
| **Question answered** | What does this resource look like? Is it compliant? | Who made this API call? What did they do? |
| **Focus** | Resource configuration state and compliance | API activity and account actions |
| **History** | Configuration change history per resource | API call history per account |
| **Evaluation** | Rules to check compliance | No evaluation — pure audit log |
| **Remediation** | Yes — SSM Automation | No |
| **Best for** | Compliance tracking, drift detection | Security auditing, forensic investigation |

**Use cases:** Compliance auditing against security standards, detecting configuration drift, resource inventory and relationship mapping, automated remediation of policy violations, cross-account compliance dashboards.
