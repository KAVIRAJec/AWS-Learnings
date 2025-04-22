## AWS Fargate

- AWS Fargate is a **serverless compute engine** for containers that allows you to run **Docker containers** without managing the underlying infrastructure. 
- It works with both Amazon ECS (Elastic Container Service) and Amazon EKS (Elastic Kubernetes Service), enabling you to focus on building and deploying applications without worrying about provisioning or scaling servers.
- Fargate consumes more cost than EC2 instances, but it is more efficient for short-lived workloads.

### Key Features
- **Serverless**
- **Pay-as-you-go pricing**
- **Automatic scaling**: Fargate automatically scales your containers based on demand, ensuring that you have the right amount of resources available at all times.
- **Secure**: Each task or pod runs in its own isolated environment.

### Components of Fargate
- **Task Definitions**: A blueprint for your application, specifying the container images, CPU and memory requirements, networking, and IAM roles.
- **Tasks**: The instantiation of a task definition. A task can run one or more containers.
- **Services**: A service is a long-running task that can be scaled up or down based on demand. It ensures that the desired number of tasks are running at all times.
- **Clusters**: A logical grouping of tasks and services. Clusters can be used to manage resources and monitor the health of your applications.

### Fargate types
- **Fargate**: The standard Fargate launch type, which allows you to run containers without managing the underlying infrastructure.
- **Fargate Spot**: A cost-effective option that allows you to run interruption-tolerant workloads at a lower price.
  - Fargate Spot uses spare capacity in the AWS cloud, which can be interrupted with a two-minute warning if the capacity is needed for other tasks.

### Use Cases
- **Microservices**
- **Batch processing**: Fargate can be used to run batch jobs without the need to manage servers.
- **CI/CD pipelines**: Fargate can be integrated into CI/CD pipelines to run tests and deploy applications.

### Fargate vs EC2 vs Lambda
| Feature       | Fargate               | EC2                          | Lambda                  |
|---------------|-----------------------|------------------------------|-------------------------|
| Management    | Serverless            | Fully managed or self-managed| Serverless              |
| Scaling       | Automatic             | Manual or Auto Scaling       | Automatic               |
| Use Case      | Containerized applications | Full control over infrastructure | Event-driven functions |
| Pricing       | Per-second billing    | Hourly billing               | Per-invocation billing   |


## Interview questions
1. **How does Fargate handle scaling?**
   - Fargate automatically scales your containers based on demand. You can set up auto-scaling policies to adjust the number of tasks running in your service based on metrics like CPU and memory usage.

2. **How to optimize costs when using Fargate?**
   - Choose the appropriate CPU and memory settings for your tasks to avoid over-provisioning resources.
   - Use spot instances: If your workload is flexible, consider using Fargate Spot to take advantage of lower prices for spare capacity.
   - Monitor usage: Use AWS CloudWatch to monitor resource usage and adjust your task definitions accordingly.