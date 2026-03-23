## AWS EKS(Elastic Kubernetes Service)

- AWS Elastic Kubernetes Service (EKS) is a fully managed Kubernetes service that simplifies the deployment, management, and scaling of containerized applications using Kubernetes on AWS. It eliminates the need to install, operate, and maintain Kubernetes control planes or nodes.
- EKS follows standard upstream Kubernetes, meaning you can use all native Kubernetes tools (kubectl, Helm, etc.).

![alt text](image.png)

### Key Features:
- **Fully Managed Control Plane**: AWS handles the Kubernetes master nodes (control plane).
- **Integration with AWS Services**: EKS integrates with other AWS services like IAM, CloudWatch, and VPC.
- **Security**: EKS provides built-in security features, including IAM roles, VPC isolation, K8S RBAC, and encryption.
- **Scalability**: EKS can automatically scale your applications based on demand using the Kubernetes Cluster Autoscaler and AWS Auto Scaling.
- **Multi-AZ Deployment**: EKS can run across multiple Availability Zones (AZs) for high availability and fault tolerance.
- **Support for Fargate**: EKS supports AWS Fargate(serverless), allowing you to run containers without managing the underlying EC2 instances.

### EKS Architecture
1. EKS consists of two main components:
  - **Control Plane**: Managed by AWS, it includes the Kubernetes API server and etcd (the key-value store for Kubernetes), Scheduler, controller-manager.
  - **Worker Nodes**: You can use EC2 instances or AWS Fargate to run your workloads. Worker node has Kubernetes components like kubelet and kube-proxy,.
2. **Networking**: EKS uses AWS VPC for networking, which is used for pod communication and service discovery. EKS supports both AWS VPC CNI plugin and third-party CNI plugins for networking.

### EKS Deployment Models
- **Self-Managed**: You manage the worker nodes (EC2 instances) and the control plane is managed by AWS.
- **Managed Node Groups**: AWS manages the worker nodes(EC2 based) for you, including scaling and updates. You define instance types and scaling.
- **Fargate**: Fully serverless. You define the CPU and memory requirements for your pods, and Fargate provisions the resources automatically.
