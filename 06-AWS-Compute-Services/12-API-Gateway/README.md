## Amazon API Gateway

Amazon API Gateway is a fully managed service for creating, publishing, and managing **REST, HTTP, and WebSocket APIs** at any scale. Acts as the "front door" for backend services like Lambda, EC2, ECS, or any HTTP endpoint.

**API Types:**
- **REST API**: Feature-rich — supports usage plans, API keys, request/response transformation, caching, and canary deployments.
- **HTTP API**: Simpler and cheaper (~70% cheaper than REST API) — best for Lambda and HTTP backends. Less features but lower latency.
- **WebSocket API**: Maintains persistent connections for real-time two-way communication (e.g., chat apps, live dashboards).

**Key Concepts:**
- **Resource & Method**: A URL path (resource) paired with an HTTP method (GET, POST, etc.) that maps to a backend integration.
- **Stage**: A named deployment snapshot (e.g., `dev`, `prod`). Each stage has its own URL and settings.
- **Versioning**: Handles API versions via stage variables or separate stages. No built-in versioning mechanism, but can manage versions through deployment strategies.
- **Cache**: API Gateway can cache responses at the stage level to improve performance and reduce backend load. Cache TTL is configurable.
- **Timeout**: Maximum integration timeout is 29 seconds for REST APIs and 30 seconds for HTTP APIs. If the backend takes longer, API Gateway returns a 504 error.
- **Integration Types**:
   - **Lambda Proxy**: Passes the full request to Lambda and returns the response as-is.
   - **HTTP Proxy**: Forwards requests to an HTTP endpoint on On premises or ALB.
   - **AWS Service**: Directly integrates with AWS services (e.g., SQS, DynamoDB) without Lambda.
- **Deployment**: Changes must be deployed to a stage to take effect.

**Features:**
- **Caching**: Cache responses at the stage level to reduce backend calls (TTL configurable).
- **Throttling**: Limit requests per second per stage or per API key to protect backends.
- **Usage Plans & API Keys**: Control and meter access for external clients.
- **CORS**: Configure cross-origin resource sharing per resource.
- **Authorizers**:
   - **IAM Authorizer**: Use AWS IAM permissions for authentication and authorization.
   - **Cognito Authorizer**: Validate JWT tokens from a Cognito User Pool.
   - **Custom Authorizer (Lambda)**: Custom auth logic via a Lambda function — used in REST APIs for third-party token validation. Adds latency and cost since every request invokes Lambda.
   - **JWT Authorizer (HTTP API only)**: Native JWT validation built into HTTP API — no Lambda needed. Automatically validates JWT tokens from any OIDC-compliant provider (Auth0, Okta, Cognito). Lower latency, lower cost than Lambda authorizer. Best choice for modern serverless APIs needing standard JWT + claim-based access control.

   | | HTTP API (JWT Authorizer) | REST API (Lambda Authorizer) | WebSocket API |
   |---|---|---|---|
   | **Auth method** | Native JWT — no code needed | Custom Lambda logic | Lambda at `$connect` only |
   | **IdP support** | Any OIDC provider (Auth0, Okta, Cognito) | Any (you write the logic) | Limited |
   | **Per-request validation** | Yes | Yes | No — only on connect, not per message |
   | **Latency / Cost** | Lower | Higher (Lambda invocation) | N/A for REST use cases |
   | **Use case** | Modern APIs with standard JWT | Complex/custom auth logic | Real-time bidirectional apps only |

**Endpoint Types:**
- **Edge-Optimized** (default): API is deployed globally and routed through **CloudFront edge locations** — reduces latency for geographically distributed clients. The API still lives in one region but requests are routed via the nearest edge.
- **Regional**: Deployed in a specific AWS region — intended for clients in the same region. Can manually combine with your own CloudFront distribution for more control over caching and distribution.
- **Private**: Only accessible from within a **VPC** via an **Interface VPC Endpoint (ENI)**. Completely isolated from the public internet — used for internal microservices.

| Type | Accessible From | Best For |
|------|----------------|----------|
| Edge-Optimized | Public internet (via CloudFront) | Global clients, low latency |
| Regional | Public internet (same region) | Region-specific APIs, custom CDN |
| Private | VPC only | Internal services, no public exposure |

**Custom Domain Name:**
- By default, API Gateway generates a URL like `https://<api-id>.execute-api.<region>.amazonaws.com/<stage>`.
- You can map a custom domain (e.g., `api.myapp.com`) using **ACM (AWS Certificate Manager)** for TLS.
- Set up a **Route 53 CNAME or Alias record** pointing to the API Gateway domain.
- **Base Path Mapping**: Map a custom domain + base path to a specific API and stage (e.g., `api.myapp.com/v1` → `my-api` prod stage, `api.myapp.com/v2` → `my-api-v2` prod stage) — useful for versioning.
- **Edge-Optimized endpoint**: Certificate must be in **us-east-1** (CloudFront requirement).
- **Regional endpoint**: Certificate must be in the **same region** as the API.

**Use cases:** Serverless APIs (with Lambda), microservice routing, WebSocket real-time apps, exposing AWS services as HTTP endpoints.
