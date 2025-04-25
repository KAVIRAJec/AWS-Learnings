## AWS Batch

- AWS Batch is a fully managed service that efficiently runs hundreds to thousands of batch computing jobs on AWS. It dynamically provisions the right compute resources (like EC2 or Spot Instances) and handles job queuing, scheduling, retries, and prioritization, so you don’t need to manage infrastructure.
- AWS Batch is automatic scalable and serverless.
- Can be used to run batch jobs on EC2, ECS, or Fargate.
- Automate the processing of large amounts of data, such as images, videos, or logs at a schedule or in response to events.

### Key Use Cases
- Data Processing
- Machine learning model training
- Image/Video Processing

### How AWS Batch Works (Simplified Flow)
1. Define a Job Definition (e.g., Docker container with memory and vCPU).
2. Submit a Job to a Job Queue.
3. AWS Batch Scheduler selects compute resources.
4. Job runs in the Compute Environment (EC2, Spot, or Fargate).
5. Logs and metrics are sent to CloudWatch.

### Types of Compute Environments
- **Managed Compute(EC2, Fargate, Spot)**
  - AWS Batch manages the compute resources for you.
  - You can specify the instance types and other configurations.
- **Unmanaged Compute Environments(EC2, Spot)**
  - You manage the compute resources yourself.
  - You can use your own EC2 instances or Fargate tasks.
- **Fargate Compute Environments(Fargate)**
  - AWS Batch provisions and manages the compute resources for you and it is fully serverless and managed by AWS.
  - You only pay for the resources you use.

### Interview Questions
1. **Can Batch supports Containerized workloads?**
    - Yes, AWS Batch is container-native. You can specify Docker images hosted in Amazon ECR, DockerHub, or other registries.

2. **How does AWS Batch scale?**
    - AWS Batch automatically scales the compute resources based on the job queue and job requirements. It can scale up when jobs are queued and scale down when jobs are complete.

3. **How does AWS Batch handle Job interruptions in spot instances?**
    - Batch supports Spot Instances, which are cost-effective but can be interrupted. AWS Batch handles Spot interruptions by automatically retrying jobs on other available instances or environments.