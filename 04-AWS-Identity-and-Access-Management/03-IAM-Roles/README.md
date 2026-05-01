#### 4. **IAM Roles**
IAM Roles are similar to users but are not associated with a specific person or application. Roles are assumed temporarily by trusted entities, such as AWS services, applications, or users from other AWS accounts.

- **Use Case**: Grant permissions to AWS services (e.g., EC2, Lambda) or enable cross-account access.
- **Example**: An EC2 instance assumes a role to access resources like S3 or DynamoDB.

#### IAM Roles vs Resource-Based Policies

Both can grant cross-account access, but they work differently.

- **IAM Role (cross-account)**: The principal **assumes** the role using STS — they temporarily give up their original permissions and operate entirely under the role's permissions. Only one set of permissions is active at a time.
- **Resource-Based Policy (cross-account)**: The policy is attached directly to the resource (e.g., S3 bucket, SQS queue, Lambda). The principal **keeps their own permissions** and gains the resource's access on top — no role assumption needed.

| | IAM Role | Resource-Based Policy |
|---|---|---|
| **Attached to** | IAM identity (assumed by principal) | The resource itself |
| **Cross-account** | Yes — principal assumes role, loses own permissions | Yes — principal retains own permissions |
| **Supported resources** | Any AWS service that supports roles | S3, SQS, SNS, Lambda, KMS, etc. (not all services) |
| **STS call needed** | Yes — `sts:AssumeRole` | No |
| **Best for** | Granting EC2/Lambda/service access, cross-account with full permission swap | S3/SQS access from another account while keeping caller's own permissions |

- **Key gotcha**: In a cross-account role assumption, the assumed role's permissions apply — even if the user's own account allows more. Use resource-based policies when you need the caller to retain their original permissions alongside the cross-account access.
