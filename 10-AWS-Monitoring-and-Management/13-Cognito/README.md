## Amazon Cognito

Amazon Cognito provides **authentication, authorization, and user management** for web and mobile applications. Has two independent components.

### Cognito User Pools (CUP):
- A fully managed **user directory** — handles sign-up, sign-in, password reset, email/phone verification, and MFA.
- Issues **JWT tokens** (ID token, access token, refresh token) on successful authentication.
- Supports **federated login**: Google, Facebook, Apple, SAML, and OIDC providers.
- Integrates with **API Gateway** and **ALB** for token-based authorization — no backend code needed to validate tokens.
- **Lambda Triggers**: Customize auth flows with Lambda (e.g., pre-signup, post-confirmation, pre-token generation).
- **Hosted UI**: Cognito provides a built-in login/signup UI — customizable with your own logo and CSS.

### Cognito Identity Pools (Federated Identities):
- Grants users **temporary AWS credentials** (via STS AssumeRoleWithWebIdentity) to access AWS services directly (e.g., S3, DynamoDB).
- Users can be authenticated (via User Pools, Google, SAML, etc.) or **unauthenticated (guest)**.
- Each authenticated/unauthenticated user is mapped to an **IAM role** — fine-grained access control per user using policy variables (e.g., `cognito-identity.amazonaws.com:sub`).
- DynamoDB: Row level access control using `cognito-identity.amazonaws.com:sub` in IAM.
**Common flow:**
1. User signs in via User Pool → receives JWT.
2. JWT exchanged at Identity Pool → receives temporary AWS credentials (via STS).
3. App uses credentials to access AWS resources directly (e.g., upload to S3).

| | User Pools | Identity Pools |
|-|-----------|---------------|
| Purpose | Authentication (who you are) | Authorization (what you can access) |
| Output | JWT tokens | Temporary AWS credentials |
| Use case | App login/signup | Direct AWS resource access |
