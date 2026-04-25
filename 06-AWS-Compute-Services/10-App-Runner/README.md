## AWS App Runner

AWS App Runner is a fully managed service that makes it easy to **deploy containerized web applications and APIs** directly from source code or a container image — no infrastructure or orchestration knowledge needed.

**Key Concepts:**
- **Source**: Deploy from a **container image** (ECR) or directly from **source code** (GitHub — supports Node.js, Python, Java, .NET, Go, PHP, Ruby).
- **Automatic build & deploy**: App Runner builds the image, deploys it, and sets up load balancing and TLS automatically.
- **Auto Scaling**: Scales up/down based on incoming requests. Can scale to zero when there's no traffic (with cold start).
- **Custom Domain**: Attach your own domain with automatic HTTPS via ACM.
- **VPC Connector**: Connect App Runner to resources inside a VPC (e.g., RDS, ElastiCache) via a VPC connector configuration.
- **Health Checks**: Built-in HTTP health checks — unhealthy instances are replaced automatically.

**App Runner vs ECS/Fargate:**
- App Runner: Fully abstracted — no task definitions, clusters, or networking config. Best for simple web apps and APIs.
- ECS/Fargate: More control — custom networking, IAM per task, sidecar containers. Best for complex microservices.

**Use cases:** REST APIs, web frontends, microservices, quick prototyping — when you want to go from code to a running HTTPS endpoint with minimal setup.
