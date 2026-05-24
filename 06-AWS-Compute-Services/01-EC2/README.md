## Amazon EC2 (Elastic Compute Cloud)

Amazon Elastic Compute Cloud (EC2) is a web service that provides resizable compute capacity in the cloud. It allows users to launch and manage virtual machines/servers (referred to as EC2 instances) in a scalable and cost-effective manner. EC2 eliminates the need for physical hardware, offering on-demand compute resources that scale based on your application needs.

### Features of Amazon EC2
- **Scalability**: Fast & easily scale up or down based on demand (using Auto Scaling and Elastic Load Balancer).
- **Flexibility & Customizable**: Choose from a variety of instance types, operating systems, memory, storage, or GPU-intensive workloads, and software packages to meet your specific needs.
- **Cost-Effective**: Pay-as-you-go pricing.
- **Security**: Provides a secure environment with features like Virtual Private Cloud (VPC), security groups, and IAM roles to control access to resources. Also provides Key Pairs for SSH access to instances.
- **Integration**: Seamlessly integrates with other AWS services like Amazon S3, RDS, Lambda, ECS, EKS, and more.
- **Static IP Addresses** (Elastic IPs): These are the fixed IP addresses that can be associated with your instances. By default, 5 elastic IP is allowed per account(request to AWS for more) This feature is not free.
- **EBS Volumes**: Persistent block storage for EC2 instances.
![alt text](image.png)

---

## Topics

| # | File | Contents |
|---|---|---|
| 1 | [Load Balancing](./01-Load-Balancing.md) | ELB types (ALB/NLB/GLB), Session Stickiness, SSL/SNI, Cross-Zone LB, Connection Draining |
| 2 | [Auto Scaling](./02-Auto-Scaling.md) | Scaling types, Cooldown, Rebalancing, Termination Policy, Instance Refresh, Lifecycle States, ASG Processes |
| 3 | [Launch Template](./03-Launch-Template.md) | Launch Template vs Launch Configuration |
| 4 | [Pricing](./04-Pricing.md) | Instance Types, Pricing Models, Reserved, Savings Plans, Spot, Dedicated Hosts, Capacity Reservations |
| 5 | [Placement Groups](./05-Placement-Groups.md) | Cluster, Spread, Partition |
| 6 | [Networking](./06-Networking.md) | ENI, ENA, EFA, SSM Session Manager |
| 7 | [Storage & AMI](./07-Storage-and-AMI.md) | Hibernate, AMI (copy, sharing encrypted), Golden AMI, User Data, Instance Store |
