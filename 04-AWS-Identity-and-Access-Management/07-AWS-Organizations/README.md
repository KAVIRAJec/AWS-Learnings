### AWS Organizations
AWS Organizations lets you create and manage multiple AWS accounts under a single organization — centralized billing, governance, and policy enforcement across all accounts.

- **Management Account**: The account that creates the organization. Has full control. Cannot be restricted by SCPs.
- **Member Accounts**: All other accounts in the org. Can be grouped into OUs.
- **Organizational Units (OUs)**: Folders for grouping accounts — e.g., `Prod`, `Dev`, `Finance`. SCPs applied to an OU inherit down to all accounts within it.
- **Service Control Policies (SCPs)**: Permission guardrails applied at the root, OU, or account level — define the **maximum permissions** any IAM entity in that account can have. SCPs operate under a deny-by-default model, meaning any action not explicitly allowed is implicitly denied(they can have allow statements) and cannot be applied to the management account.
  - To remove an account from the org, it must first be made a standalone account.
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
