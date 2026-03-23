## Amazon ECS (Elastic Container Service)

- Amazon ECS is a fully managed container orchestration service by AWS that lets you easily run, manage, and scale Docker containers. It handles the deployment, management, and scaling of containerized applications using clusters of EC2 instances or serverless compute with AWS Fargate.
- A fully managed service by AWS that simplifies running and managing Docker containers on a cluster of virtual machines.

![alt text](image.png)
### Key Concepts:
- **Cluster**: A logical grouping of tasks or services. You can run multiple clusters in a single AWS account.
- **Task Definition**: A blueprint(abstraction) for your application. It describes one or more containers that form your application. It is a JSON file that describes the containers, their resources, and how they should run.
- **Task**: An implementation of a task definition. It runs on a cluster and can be managed by ECS.
- **Service**: A long-running task that is managed by ECS. It ensures that the desired number of tasks are running and can be scaled up or down.

### Launch Types:
- **EC2 Launch Type**: You manage the EC2 instances in your cluster. You have full control over the underlying infrastructure. You need to manually provision and manage the EC2 instances that run your containers.
- **Fargate Launch Type**: AWS manages the infrastructure for you. You only need to define your application and its requirements (like CPU and memory). Fargate automatically provisions the compute resources needed to run your containers.
