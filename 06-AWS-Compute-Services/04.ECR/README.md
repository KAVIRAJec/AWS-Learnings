## Amazon ECR (Elastic Container Registry)

Amazon ECR is a fully managed **Docker container image registry** — store, manage, and deploy container images securely. Integrated natively with ECS, EKS, and Fargate.

**Key Concepts:**
- **Private Registry**: Each AWS account gets a private registry. Access controlled via **IAM policies**.
- **Public Registry**: Share images publicly via [gallery.ecr.aws](https://gallery.ecr.aws) — similar to Docker Hub.
- **Repository**: Stores multiple versions (tags) of a container image.
- **Image Scanning**: Automatic vulnerability scanning on push using **Amazon Inspector** — detects OS and package-level CVEs.
- **Lifecycle Policies**: Automatically delete old or untagged images based on rules (e.g., keep only last 10 images) — reduces storage cost.
- **Cross-region / Cross-account**: Images can be replicated to other regions or accounts for availability and DR.
- **Encryption**: Images encrypted at rest using KMS. In-transit via HTTPS.

**Pricing:** Pay for storage (GB/month) and data transfer out. No charge for data transfer within the same region to ECS/EKS/Fargate.
