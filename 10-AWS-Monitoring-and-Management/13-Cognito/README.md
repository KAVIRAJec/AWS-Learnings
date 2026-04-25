## Amazon Cognito

Amazon Cognito provides **authentication, authorization, and user management** for web and mobile applications.

**Two Components:**
- **User Pools**: A user directory that handles sign-up, sign-in, MFA, and token issuance (JWT). Supports social login (Google, Facebook, Apple) and SAML/OIDC federation.
- **Identity Pools (Federated Identities)**: Grants users **temporary AWS credentials** (via STS) to access AWS services directly (e.g., S3, DynamoDB). Works with User Pools or external identity providers.

**Common flow:** User signs in via User Pool → gets JWT → exchanges JWT at Identity Pool → gets AWS credentials → accesses AWS resource directly.
