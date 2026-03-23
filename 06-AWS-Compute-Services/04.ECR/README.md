## Elastic Container Registry (ECR)

ECR is a fully managed Docker container registry by AWS. It allows you to store, manage, share, and deploy container images securely and at scale. It's deeply integrated with ECS, EKS, and Fargate, and supports standard Docker CLI commands.

**Key Features:**
- Fully managed and highly available. It is similar to Docker Hub but is private and secure.Supports both public and private repositories.
- Supports Docker CLI commands, making it easy to push and pull images.
- Supports tagging container images and version management.
- Lifecycle policies for automatic image cleanup of old images.
- Image scanning for vulnerabilities to ensure security compliance.
- Pay for Storage and data transfer.

**Use Cases:**
- Storing and managing Docker images for applications running on ECS, EKS, or Fargate.
- Enabling CI/CD pipelines to build, test, and deploy containerized applications.
- AWS native container registry for organizations using AWS services.
