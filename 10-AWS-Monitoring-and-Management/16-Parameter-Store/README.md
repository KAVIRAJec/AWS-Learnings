## AWS Systems Manager Parameter Store

Parameter Store is a secure, hierarchical storage for **configuration data and secrets** — part of AWS Systems Manager.

**Key Concepts:**
- Parameters organized in a hierarchy: `/myapp/prod/db-password`.

**Parameter Data Types:**
- **String**: Plain text — any single value. e.g., a DB hostname, an environment name, a version number. No encryption.
- **StringList**: Comma-separated list of plain-text values stored as one parameter. e.g., `"us-east-1,us-west-2,eu-west-1"`. Useful for lists of allowed regions, IP ranges, or feature flags. No encryption.
- **SecureString**: Encrypted value using a KMS key (AWS Managed or Customer Managed). Use for passwords, API keys, tokens, or any sensitive data. Decrypted automatically when fetched with the `WithDecryption=true` flag — caller needs both SSM and KMS permissions.

| | String | StringList | SecureString |
|---|---|---|---|
| **Value** | Single plain-text value | Comma-separated values | Encrypted single value |
| **Encryption** | No | No | Yes (KMS) |
| **Use for** | Config, hostnames, env names | Lists of values | Passwords, secrets, tokens |
| **KMS needed** | No | No | Yes (to decrypt) |
- **Versioning**: Every update creates a new version; you can reference specific versions.
- **No automatic rotation** — unlike Secrets Manager, rotation must be handled manually or via Lambda.
- **Standard vs Advanced parameters** (per account per region):

| | Standard | Advanced |
|---|---|---|
| **Max parameters** | 10,000 | 100,000 |
| **Max value size** | 4 KB | 8 KB |
| **Parameter policies** | No | Yes |
| **Cost** | Free | $0.05 per parameter/month |
- Integrated with EC2, Lambda, ECS, CloudFormation, and CodeDeploy.

**Parameter Policies** (Advanced parameters only): Assign TTL and notification rules to a parameter. Multiple policies can be attached at once.
- **Expiration** — auto-deletes the parameter at a set timestamp. Forces rotation of sensitive data like passwords.
- **ExpirationNotification** — sends an EventBridge event N days **before** expiry (e.g., `"Before": "15", "Unit": "Days"`).
- **NoChangeNotification** — sends an EventBridge event if parameter hasn't been updated in N days (e.g., `"After": "20", "Unit": "Days"`). Useful to detect stale credentials.

**Use cases:** Store environment variables, feature flags, DB connection strings, license keys.

---

## Parameter Hierarchy

Parameters are stored in a **folder-like path structure** using `/` as a separator — up to 15 levels deep.

```
/my-department/
  my-app/
    dev/
      db-url
      db-password
    prod/
      db-url
      db-password
  other-app/

/other-department/
```

- Fetch a single parameter: `GetParameters` API with the full path.
- Fetch all parameters under a path: `GetParametersByPath` API with `/my-department/my-app/dev/` — returns all parameters under that prefix in one call.

---

## IAM Access at the Path Level

IAM policies can be scoped to a **specific path prefix** — so each service, environment, or team only has access to its own parameters, not the entire Parameter Store.

```json
{
  "Effect": "Allow",
  "Action": ["ssm:GetParameters", "ssm:GetParametersByPath"],
  "Resource": "arn:aws:ssm:us-east-1:123456789012:parameter/my-department/my-app/dev/*"
}
```

---

## Special Public Parameters

AWS maintains publicly readable parameters you can reference directly — no cost, always up to date.

**Latest AMI IDs:**
- Path: `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2`
- AWS updates this automatically whenever a new Amazon Linux 2 AMI is released.
- Use in CloudFormation or scripts to always launch from the latest AMI without hardcoding AMI IDs per region.

```bash
# Fetch the latest Amazon Linux 2 AMI ID
aws ssm get-parameters \
  --names /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
  --query "Parameters[0].Value"
```

**Secrets Manager Reference:**
- Path: `/aws/reference/secretsmanager/<secret_ID_in_Secrets_Manager>`
- Allows you to fetch a Secrets Manager secret **through the Parameter Store API** — no code change needed if your app already uses SSM.
- Read-only — you cannot write back to Secrets Manager via this path.
- The caller still needs `secretsmanager:GetSecretValue` permission in addition to SSM access.
