## AWS Secrets Manager

AWS Secrets Manager securely **stores, manages, and automatically rotates** sensitive information like database credentials, API keys, OAuth tokens, and passwords — applications retrieve secrets at runtime, no hardcoding.

- All secrets encrypted at rest using **KMS** (AWS Managed or Customer Managed key).
- Charged **$0.40 per secret/month** + **$0.05 per 10,000 API calls**.

---

## Key Concepts

- **Secret**: A key-value pair (or JSON blob) stored encrypted — e.g., `{ "username": "admin", "password": "s3cr3t" }`.
- **Secret Version**: Every rotation or update creates a new version. Versions are labeled with **staging labels**:
  - `AWSCURRENT` — the version currently in use.
  - `AWSPREVIOUS` — the previous version (kept for rollback).
  - `AWSPENDING` — the new version being rotated in (during rotation window).
- **Resource Policy**: Attach a resource-based policy to a secret for cross-account access — another account's Lambda or app can retrieve the secret without assuming a role.

---

## Automatic Rotation

Secrets Manager rotates secrets **automatically on a schedule** using a Lambda function — the app always fetches the latest secret at runtime and is never aware of the rotation.

- **Native rotation support**: RDS (MySQL, PostgreSQL, Oracle, SQL Server), Aurora, Redshift, DocumentDB — AWS provides the rotation Lambda automatically.
- **Custom rotation**: For any other secret (API key, OAuth token) — you provide the Lambda function with the rotation logic.
- **Rotation schedule**: Set in days (e.g., every 30 days) or using a cron expression.
- **Zero-downtime rotation**: Secrets Manager writes the new credential to `AWSPENDING`, validates it works, then promotes it to `AWSCURRENT` — old credential stays in `AWSPREVIOUS` briefly for in-flight requests.

---

## Multi-Region Secrets

- Replicate a secret to one or more **replica regions** — same secret, kept in sync automatically.
- Replica is a read replica — updates to the primary propagate to all replicas.
- **Use case**: Multi-region applications or disaster recovery — each region's app reads from its local replica for low latency, and failover promotes the replica to a standalone secret.

---

## Secrets Manager vs Parameter Store

| | Secrets Manager | Parameter Store |
|---|---|---|
| **Purpose** | Purpose-built for secrets | Config data + secrets |
| **Automatic rotation** | Yes — native + custom Lambda | No — manual or custom Lambda |
| **Encryption** | Always (KMS) | Optional (SecureString only) |
| **Cost** | $0.40/secret/month | Free (standard) |
| **Max value size** | 64 KB | 4 KB (standard), 8 KB (advanced) |
| **Versioning** | Yes — with staging labels | Yes — numeric versions |
| **Cross-account** | Yes — resource policy | Yes(advanced parameters), No cross region |
| **Best for** | DB credentials, API keys needing rotation | App config, feature flags, non-rotating secrets |
