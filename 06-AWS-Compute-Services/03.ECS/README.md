## Amazon ECS (Elastic Container Service)

Amazon ECS is a fully managed **container orchestration service** that runs, manages, and scales Docker containers on AWS — either on EC2 instances you manage or serverless via Fargate.

![alt text](image.png)

**Key Concepts:**
- **Cluster**: Logical grouping of tasks/services. Can use EC2, Fargate, or both.
- **Task Definition**: A JSON blueprint describing one or more containers — image, CPU, memory, port mappings, environment variables, IAM role, volumes.
- **Task**: A running instance of a task definition. Can be a one-off job or part of a service.
- **Service**: Ensures a specified number of tasks are always running. Integrates with ALB for load balancing and supports auto scaling.
- **ECS Container Agent**: Runs on each EC2 instance in the cluster to register the instance with ECS and communicates with ECS control plane to manage tasks.

**Launch Types:**

| | EC2 Launch Type | Fargate Launch Type | ECS Anywhere |
|-|----------------|---------------------|--------------|
| Infrastructure | You manage EC2 instances | AWS manages compute | Your own servers (on-prem, VMs, other clouds) |
| Control | Full control (instance type, OS) | No access to underlying host | Full control — your hardware |
| Cost | Pay for EC2 instances & storage volumes (even idle) | Pay per task vCPU/memory used | Pay per managed external instance/hour |
| Use case | Predictable workloads, GPU, custom OS | Serverless, variable workloads | Extend ECS to on-premises or hybrid environments |

**ECS Anywhere:**
- Run ECS tasks on **your own on-premises servers, VMs, or instances in other clouds** — managed from the same ECS control plane.
- Register external instances with ECS using the **SSM Agent + ECS Agent** — AWS manages orchestration, you manage the physical/virtual hardware.
- Use case: Regulatory requirements to keep data on-premises, hybrid deployments, extending containerized workloads to edge locations.
- **No VPC required** for external instances — communicate with AWS over the internet or Direct Connect.
- CloudWatch Application Insights integrates with ECS Anywhere clusters for observability and troubleshooting.

**IAM Roles:**
- **EC2 Instance Profile**: Used by the container agent on EC2 — allows to pull images(ECR), write logs(Cloudwatch), make container API calls(ECS) etc.
- **ECS Task Role**: IAM role assigned per task definition — allows the container itself to access AWS services (e.g., S3, DynamoDB).

**Storage:**
- **Ephemeral storage**: Temporary storage tied to the task lifecycle (Fargate: up to 200 GB).
- **EFS**: Mount EFS volumes for persistent shared storage across tasks (works with both EC2 and Fargate).
- **EBS**: Can be attached per task on EC2 launch type.

**Auto Scaling:**

ECS has two independent levels of scaling:

**1. Service Auto Scaling** — adjusts the **number of tasks** (containers) in a service.

Supported metrics for ECS Service scaling:
- `ECSServiceAverageCPUUtilization` — average CPU utilization across all tasks in the service
- `ECSServiceAverageMemoryUtilization` — average memory utilization across all tasks in the service
- `ALBRequestCountPerTarget` — number of requests per target in an ALB target group

> **ALB does not expose a CPU utilization metric** — you cannot create an Auto Scaling policy based on "ALB CPU utilization." Only the above three metrics are available for ECS Service Auto Scaling.

**2. Cluster Auto Scaling** — adjusts the **number of EC2 instances** in the cluster (EC2 launch type only). Can be driven by service-level CPU utilization triggering the need for more capacity.
- **Auto Scaling Groups**: Add/remove EC2 instances based on CloudWatch alarms.
- **ECS Cluster Capacity Providers**: ECS manages EC2 scaling automatically based on task demand.

**Scaling policy types (apply to both levels):**
- **Target Tracking**: Maintain a specific metric at a target value (e.g., keep CPU at 50%).
- **Step Scaling**: Trigger on a CloudWatch alarm threshold (e.g., scale out if CPU > 70% for 5 minutes).
- **Scheduled Scaling**: Scale on a time schedule (e.g., scale out at 9 AM, scale in at 5 PM).


**Integrations:**
- **Load Balancer**:
    - **ALB**: Distributes traffic across tasks using dynamic port mapping.
    - **NLB**: Only for high-performance and throughput-intensive, low-latency workloads.
    - **Classic ELB**: Not recommended for new applications, only supports static port mapping.(Not works with Fargate)
- **ECR**: Pull container images from Amazon ECR.
- **CloudWatch**: Logs and metrics from containers.
- **Auto Scaling**: Scale tasks based on CPU/memory or custom CloudWatch metrics.
