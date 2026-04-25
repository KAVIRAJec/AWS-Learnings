## AWS App2Container (A2C)

AWS App2Container is a **command-line tool** that containerizes existing Java and .NET applications running on-premises or on EC2 — without modifying the application code.

**How it works:**
1. **Discover**: A2C scans the server and identifies running Java/.NET applications and their dependencies.
2. **Analyze**: Analyzes the app's runtime environment, ports, config, and dependencies.
3. **Containerize**: Packages the app into a Docker image and pushes it to **ECR**.
4. **Deploy**: Generates deployment artifacts for **ECS**, **EKS**, or **App Runner** — including task definitions, Kubernetes manifests, and CloudFormation/CDK templates.

**Key Concepts:**
- Supports **Java** (Tomcat, Spring) and **.NET** (IIS) applications.
- No code changes required — lifts the app as-is into a container.
- Can run on the app server directly or in a **remote worker mode** (agent on server, orchestration elsewhere).
- Generated CloudFormation templates include networking (VPC, subnets, ALB) and CI/CD pipeline setup.

**Use cases:** Migrating legacy on-premises or EC2-hosted apps to containers without a rewrite.
