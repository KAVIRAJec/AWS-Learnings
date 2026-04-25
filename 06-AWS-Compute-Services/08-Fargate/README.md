## AWS Fargate

AWS Fargate is a **serverless compute engine for containers** — runs Docker containers without provisioning or managing EC2 instances. Works with both **ECS** and **EKS**.

**How it works:**
- You define CPU and memory at the task/pod level, not at the instance level.
- AWS provisions the right compute, runs the container, and tears it down when done.
- Each task runs in its own **isolated compute environment** (no shared kernel with other tasks).

**Key Concepts:**
- **No EC2 to manage**: No instance patching, no cluster capacity planning, no SSH access to nodes.
- **Per-task billing**: Pay only for the vCPU and memory allocated to each task, billed per second.
- **Networking**: Each Fargate task gets its own **ENI (Elastic Network Interface)** and private IP — runs in `awsvpc` network mode.
- **IAM Task Role**: Assign an IAM role per task definition to grant the container access to AWS services.
- **Storage**: Up to 20 GB ephemeral storage by default, configurable up to 200 GB. EFS supported for persistent shared storage.

**Fargate vs Fargate Spot:**
- **Fargate**: Standard — always-on, suitable for production workloads.
- **Fargate Spot**: Uses spare AWS capacity at a lower cost (~70% discount). Can be interrupted with a **2-minute warning** — suitable for fault-tolerant, batch, or non-critical workloads.

**Use cases:** Microservices, batch jobs, CI/CD task runners, event-driven workloads, any containerized app where you don't want to manage servers.
