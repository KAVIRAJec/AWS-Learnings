## AWS Systems Manager Parameter Store

Parameter Store is a secure, hierarchical storage for **configuration data and secrets** — part of AWS Systems Manager.

**Key Concepts:**
- Stores plain-text config values or **SecureString** (encrypted via KMS).
- Parameters organized in a hierarchy: `/myapp/prod/db-password`.
- **Versioning**: Every update creates a new version; you can reference specific versions.
- **No automatic rotation** — unlike Secrets Manager, rotation must be handled manually or via Lambda.
- Free tier available (standard parameters). Advanced parameters (larger size, higher throughput) are paid.
- Integrated with EC2, Lambda, ECS, CloudFormation, and CodeDeploy.

**Use cases:** Store environment variables, feature flags, DB connection strings, license keys.
