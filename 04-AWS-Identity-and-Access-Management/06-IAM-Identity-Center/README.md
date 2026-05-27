#### 6. **IAM Identity Center**
IAM Identity Center (formerly AWS SSO) is a centralized service to manage **SSO access** across multiple AWS accounts and business applications (Salesforce, Slack, Microsoft 365, etc.) from a single place.

**Key Concepts:**
- **Identity Source**: Where user identities live — options:
  - **IAM Identity Center directory** (built-in, default) — create and manage users/groups directly.
  - **Active Directory** — sync existing corporate users. Three types:
    - **AWS Managed Microsoft AD**: Fully managed real Microsoft AD hosted in AWS, runs inside your VPC across 2 AZs.
      - **Directory-aware workloads**: Some apps (SQL Server, FSx for Windows, SharePoint) are built to authenticate users and apply permissions using AD natively — they can't use IAM users. These are called directory-aware. AWS Managed AD lets you run them in AWS without setting up your own AD servers.
      - **Trust relationship**: A handshake between two AD domains so users from one domain can access resources in the other — without a separate account. Example: on-premises AD (`corp.local`) ↔ AWS Managed AD (`aws.corp.com`) — once trust is set up, your existing on-prem users can SSO into AWS-side resources using the same credentials. No duplicate accounts needed.
      - Best for large orgs that want full AD in the cloud, need directory-aware workloads, or want SSO bridging on-premises and AWS.
    - **AD Connector**: A proxy gateway that redirects authentication requests to your **existing on-premises AD** — no data is cached or stored in AWS. Users authenticate against on-premises AD directly. Best when your org policy requires all identities to stay on-premises. Requires a working network connection (Direct Connect or VPN) to on-premises.
    - **Simple AD**: Standalone, low-cost AD-compatible directory powered by **Samba 4** — not Microsoft AD. Supports basic AD features (user accounts, group policies, domain join). Does **not** support MFA, trust relationships, or RDS SQL Server. Best for small orgs that need basic AD without on-premises infrastructure.
  - **External IdP** (Okta, Azure AD, Ping) — federate via SAML 2.0.
- **Permission Sets**: A collection of IAM policies defined once in Identity Center and assigned to users/groups per AWS account. Identity Center creates and manages the underlying IAM role in each account automatically.
- **Account Assignment**: Maps a user/group + permission set to a specific AWS account — controls who can access which account with what permissions.
- **AWS Access Portal**: The web portal (`https://<subdomain>.awsapps.com/start`) where users log in once and see all their assigned AWS accounts and applications — click to assume the role, no per-account login needed.
- **SCIM**: Automatic user/group provisioning and deprovisioning from external IdPs — no manual sync needed.

**IAM Identity Center vs IAM Users:**

| | IAM Identity Center | IAM Users |
|---|---|---|
| **Best for** | Multi-account, human workforce access | Single account, programmatic/service access |
| **Credentials** | Short-lived SSO session tokens | Long-lived access keys or passwords |
| **Management** | Centralized — one place for all accounts | Per-account — manage in each account separately |
| **MFA** | Centralized MFA policy | Per-user MFA setup |
| **IdP integration** | Native (Okta, Azure AD, AD) | Not supported |
