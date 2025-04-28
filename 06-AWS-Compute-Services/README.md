## Computing Services By AWS

- EC2 (Elastic Compute Cloud): Virtual servers in the cloud.
- Lambda: Serverless compute service that runs code in response to events.
- ECS (Elastic Container Service): Container orchestration service for Docker containers.
- ECR (Elastic Container Registry): Managed Docker container registry.
- LightSail: Simplified cloud platform for developers to build applications.
- Batch: Fully managed batch processing at any scale.
- EKS (Elastic Kubernetes Service): Managed Kubernetes service for running containerized applications.
- Fargate: Serverless compute engine for containers that works with ECS and EKS.
- Elastic Beanstalk: Platform as a Service (PaaS) for deploying and managing applications.

## Some Common Concepts:
1. **AWS Compute Optimizer**
    - AWS Compute Optimizer is a service that recommends optimal AWS resources for your workloads to reduce costs and improve performance.
    - It analyzes your resource configurations and utilization metrics to provide recommendations for EC2 instances, Auto Scaling groups, Lambda functions, and EBS volumes.
    - AWS Compute Optimizer does not work with S3, DynamoDB, EFS, or RDS.
    - The service uses machine learning algorithms to analyze historical usage patterns and make recommendations based on best practices.
    - AWS Compute Optimizer helps you right-size your resources, ensuring that you are using the most cost-effective options for your workloads.