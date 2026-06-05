## AWS STS (Security Token Service)

AWS STS is a **global web service** that issues **temporary, short-lived security credentials** — used whenever an identity needs to act as something else temporarily (assume a role, federate an external user, use MFA).

Temporary credentials have three components:
- **Access Key ID** — identifies the credential
- **Secret Access Key** — signs API requests
- **Session Token** — must be passed alongside every API call; proves the credential is temporary

Credentials expire automatically (configurable duration) — no need to rotate them manually.

---

## STS API Calls

### `AssumeRole`
- An IAM user, AWS service (EC2, Lambda), or another account assumes an IAM role.
- Returns temporary credentials valid for **15 minutes to 12 hours** (default 1 hour).
- **Cross-account**: Account A user assumes a role in Account B → gets Account B permissions.
- **Same-account**: Lambda function assumes a role to get scoped-down permissions.

```
IAM User / Service
        │
        │  sts:AssumeRole (role ARN)
        ▼
      AWS STS
        │
        │  returns: Access Key + Secret + Session Token
        ▼
  Temporary credentials (valid for configured duration)
  → caller now acts as the role, loses their own permissions
```

### `AssumeRoleWithSAML`
- Federation via **SAML 2.0** — for corporate identity providers (Active Directory, ADFS, Okta with SAML).
- User logs into their corporate IdP → IdP issues a SAML assertion → exchanged for temporary AWS credentials via STS.
- No IAM user needed for each employee — map SAML groups to IAM roles.

### `AssumeRoleWithWebIdentity`
- Federation via **web identity** — for external IdPs using OIDC (Google, Facebook, Amazon, any OIDC provider).
- App gets an ID token from the IdP → exchanges it with STS for temporary AWS credentials.
- **Recommended approach**: Use **Amazon Cognito Identity Pools** instead of calling `AssumeRoleWithWebIdentity` directly — Cognito handles token exchange, supports multiple IdPs, and adds anonymous user support.

### `GetSessionToken`
- Returns temporary credentials for an **IAM user** — mainly used to satisfy **MFA requirements**.
- When an IAM policy has an MFA condition (`aws:MultiFactorAuthPresent: true`), the user must first call `GetSessionToken` with their MFA token to get temporary credentials that include MFA context.
- Duration: **15 minutes to 36 hours**.

### `GetFederationToken`
- Issues temporary credentials for a **federated user** (no IAM identity) — e.g., a broker application that assigns permissions to external users.
- Longer duration (up to 36 hours).
- Less common — `AssumeRole` + `AssumeRoleWithWebIdentity` are preferred in most cases.

---

## STS API Comparison

| API | Who calls it | IdP / mechanism | Duration | Use case |
|---|---|---|---|---|
| `AssumeRole` | IAM user / AWS service / role | IAM role trust policy | 15 min – 12 hr | Cross-account, service roles, same-account scoping |
| `AssumeRoleWithSAML` | SAML assertion holder | SAML 2.0 IdP (AD, ADFS) | 15 min – 12 hr | Corporate SSO federation |
| `AssumeRoleWithWebIdentity` | OIDC token holder | Google, Facebook, OIDC | 15 min – 12 hr | Mobile/web app user identity (use Cognito instead) |
| `GetSessionToken` | IAM user | MFA device | 15 min – 36 hr | Satisfying MFA conditions |
| `GetFederationToken` | Application broker | None (application assigns policy) | 15 min – 36 hr | Custom brokered federation |

---

## Key Properties

- **STS is global** — single endpoint (`sts.amazonaws.com`) but also available as regional endpoints for lower latency and higher availability.
- **No stored credentials** — STS does not store the temporary credentials it issues. Once expired, they are gone.
- **Session policies**: You can pass an inline session policy when calling `AssumeRole` to further restrict the role's permissions for that session — permissions are the intersection of the role's policy and the session policy.
- **Condition keys**: CloudTrail logs all STS API calls — you can audit who assumed what role, from where, and when.
