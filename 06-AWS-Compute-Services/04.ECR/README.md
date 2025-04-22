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

**Interview Questions:**
1. **What is ECR and how does it differ from ECS?**
    - ECR is a container registry service for storing and managing Docker images, while ECS is a container orchestration service for running and managing containers. ECS pulls images from ECR to deploy them.

2. **How do you push an image to ECR?**
    - Authenticate Docker to the ECR registry using the AWS CLI, tag the image with the ECR repository URI, and then push the image using Docker CLI commands.

3. **What are lifecycle policies in ECR?**
    - Lifecycle policies allow you to define rules for automatically transitioning or expiring images based on criteria like number of images, helping manage storage costs and keep the repository clean.

4. **What is image scanning in ECR?**
    - Image scanning is a feature that automatically scans container images for vulnerabilities in software dependencies when they are pushed to ECR using **Amazon Inspector**. Scanning can be done manually.

5. **How does ECR work with multi region deployments?**
    - ECR supports cross-region replication, allowing you to replicate images across multiple AWS regions for faster access and low latency. You can set up replication rules to automatically copy images to other regions.