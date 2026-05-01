#### 3. **IAM Policies**
IAM Policies are JSON documents that define what actions are allowed or denied on specific resources. Policies can be attached to IAM users, groups, or roles, and they govern the permissions that are granted.

- **Types of Policies**:
  1. **AWS Managed Policies**: Predefined policies created and maintained by AWS.
  2. **Customer Managed Policies**: Custom policies created by users for specific use cases.
  3. **Inline Policies**: Policies embedded directly into a user, group, or role.

- **Policy Structure**: 
 - **Version & Id** Version is policy language version, and Id is an optional identifier for the policy.
 - **Statement**: Contains one or more individual statements that define permissions.
  - **Sid**: An optional identifier for the statement.
  - **Effect**: Specifies whether the statement allows or denies access (Allow or Deny).
  - **Principal**: Specifies the user, group, or role to which the policy applies.
  - **Action**: Lists the actions that are allowed or denied (e.g., s3:ListBucket, ec2:StartInstances).
  - **Resource**: Specifies the specific AWS resources to which the actions apply (e.g., arn:aws:s3:::example-bucket).
- **Example Policy**:
```json
{
  "Version": "2012-10-17",
  "Id": "ExamplePolicy",
  "Statement": [
    {
      "Sid": "AllowListBucket",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/ExampleUser"
      },
      "Action": ["s3:ListBucket", "s3:GetObject", "s3:Get*"],
      "Resource": "arn:aws:s3:::example-bucket"
    }
  ]
}
```
- **`aws:PrincipalOrgID` Condition Key**: Restricts access to only principals (users, roles) that belong to a specific AWS Organization — useful in S3 bucket policies to allow access from any account in your org without listing every account ID.
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowOrgAccess",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalOrgID": "o-xxxxxxxxxx"
        }
      }
    }
  ]
}
```
  - `Principal: "*"` with `aws:PrincipalOrgID` means "anyone, but only if they're in my org" — prevents external AWS accounts from accessing the bucket.
  - To restrict further to a specific OU, use `aws:PrincipalOrgPaths`: `"o-xxxxxxxxxx/r-xxxx/ou-xxxx-yyyyyyyy/*"` — matches all accounts under that OU.

- **IAM Permission Boundaries**: A managed policy attached to an IAM user or role that defines the **maximum permissions** that identity can ever have — even if their identity-based policies allow more, the boundary caps it.
  - The effective permissions = **intersection** of the identity-based policy AND the permission boundary. If either doesn't allow an action, it's denied.
  - Applied per user/role (not at account or OU level — that's SCPs).
  - **Key use case**: Safely delegate IAM creation to developers — attach a boundary so they can only create roles/users with permissions up to their own boundary, preventing privilege escalation.
  - Does **not** apply to resource-based policies or SCPs — only limits what the identity-based policy can grant.

| | Permission Boundary | SCP |
|---|---|---|
| **Applies to** | Individual IAM user or role | All IAM identities in an account/OU |
| **Set by** | Account admin | Org management account |
| **Limits** | Max permissions of a specific identity | Max permissions of any identity in the account |
| **Grants permissions** | No — only restricts | No — only restricts |
