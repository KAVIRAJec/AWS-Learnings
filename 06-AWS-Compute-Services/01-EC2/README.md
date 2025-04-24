## Amazon EC2 (Elastic Compute Cloud)

Amazon Elastic Compute Cloud (EC2) is a web service that provides resizable compute capacity in the cloud. It allows users to launch and manage virtual machines/servers (referred to as EC2 instances) in a scalable and cost-effective manner. EC2 eliminates the need for physical hardware, offering on-demand compute resources that scale based on your application needs.

### Features of Amazon EC2
- **Scalability**: Fast & easily scale up or down based on demand (using Auto Scaling and Elastic Load Balancer).
- **Flexibility & Customizable**: Choose from a variety of instance types, operating systems, memory, storage, or GPU-intensive workloads, and software packages to meet your specific needs.
- **Cost-Effective**: Pay-as-you-go pricing.
- **Security**: Provides a secure environment with features like Virtual Private Cloud (VPC), security groups, and IAM roles to control access to resources. Also provides Key Pairs for SSH access to instances.
- **Integration**: Seamlessly integrates with other AWS services like Amazon S3, RDS, Lambda, ECS, EKS, and more.
- **Static IP Addresses** (Elastic IPs): This feature is not free.
- **EBS Volumes**: Persistent block storage for EC2 instances.

### EC2 Instance Types
EC2 instances are categorized into different families based on their use cases. 
- **General Purpose**: Balanced compute, memory, and networking resources. Examples: T3, M5, M6g.(Application: web servers, code repositories, etc.)
- **Compute Optimized**: High-performance processors for compute-intensive tasks. Examples: C5, C6g.(Application: Game servers, etc.)
- **Memory Optimized**: Designed for memory-intensive applications. Examples: R5, R6g.(Application: high-performance databases, in-memory analytics etc.)
- **Storage Optimized**: High disk throughput and IOPS for data-intensive applications. Examples: I3, D2.(Application: NoSQL databases, data warehousing, etc.)
- **Accelerated/GPU Intensive Computing**: GPU-based instances for machine learning and graphics-intensive applications. Examples: P3, G4.(Application: Machine learning, 3D rendering, etc.)

### EC2 Pricing Models
- **On-Demand Instances**: Pay-per-use pricing for instances that are launched and terminated as needed. Based on the number of seconds/hours the instance is running.
- **Reserved Instances**: Commit to a one or three-year term for discounted pricing. It is kind of booking an instance for a specific period.(Applies discount upto 70%)
- **Spot Instances**: Purchase unused EC2 capacity at reduced rates. Ideal for short-term, flexible workloads. Spot instances can be interrupted by AWS with two-minute warning if the capacity is needed elsewhere.(Applies discount upto 90%)
- **Dedicated Hosts**: Physical servers dedicated to your use, providing control over how instances are placed on the server. Allows you to use software licenses. (Applies discount upto 70%)

## Interview Questions
1. **Difference between EC2 and Lambda?**
   - EC2 is a virtual server that you manage, while Lambda is a serverless compute service that runs code in response to events without provisioning or managing servers.
   - EC2 is suitable for long-running applications, while Lambda is ideal for short-lived tasks or event-driven applications.

2. **What is AMI? Can you create your own AMI?**
   - AMI (Amazon Machine Image) is a pre-configured template that contains the operating system, application server, and applications required to launch an EC2 instance.
   - Yes, you can create your own AMI from an existing EC2 instance by creating a **snapshot** or use AWS Marketplace to find pre-built AMIs.

3. **What is the difference between EBS and Instance Store?**
   - EBS (Elastic Block Store) is a persistent block storage service that can be attached to EC2 instances, while Instance Store is temporary storage that is physically attached to the host (EC2) server and data is lost when the instance is stopped or terminated.
   - EBS volumes can be backed up using snapshots, while Instance Store data cannot be recovered after instance termination(Shutdown).
   - EBS is suitable for data that needs to persist beyond the life of the instance, while Instance Store is suitable for temporary data or caching.

4. **EC2 User Data Scripts VS EC2 Metadata?**
   - **User Data Scripts**: Scripts that run when an instance is launched. They are used to perform common automated configuration tasks and run only once at launch time.(e.g., installing software, configuring settings)
   - **EC2 Metadata**: Provides information about the instance itself, such as instance ID, public IP address, and security groups. It can be accessed from within the instance using a special URL (http://169.254.169.254/latest/meta-data/).

5. **EC2 Security Groups vs NACLs?**
   - **Security Groups**: Act as a virtual firewall for your EC2 instances to control inbound and outbound traffic. They are stateful, meaning if you allow an incoming request, the response is automatically allowed.
   - **NACLs (Network Access Control Lists)**: Act as a firewall for controlling traffic in and out of one or more subnets. They are stateless, meaning you must explicitly allow both inbound and outbound traffic.

   - **Security Groups are associated with EC2 instances, while NACLs are associated with subnets in a VPC.**

6. **EC2 Auto Scaling VS Load Balancing?**
   - **Auto Scaling**: Automatically adjusts the number of EC2 instances in a group based on demand. It ensures that you have the right number of instances running to handle the load.
   - **Load Balancing**: Distributes incoming traffic across multiple EC2 instances to ensure no single instance is overwhelmed. It improves availability and fault tolerance.
   - **Auto Scaling is about scaling the number of instances, while Load Balancing is about distributing traffic among those instances.**
   - **Auto Scaling can work with Load Balancers to ensure that new instances are automatically registered with the load balancer.**

7. **Types of Load Balancers**
   - **Application Load Balancer (ALB)**: Operates at the application layer (Layer 7) and is ideal for HTTP/HTTPS traffic. It can route requests based on content and supports WebSocket and HTTP/2.(Application: microservices, containerized applications, etc.)
   - **Network Load Balancer (NLB)**: Operates at the transport layer (Layer 4) and is designed for high-performance TCP/UDP traffic. It can handle millions of requests per second with low latency.(Application: real-time applications, gaming, etc.)
   - **Classic Load Balancer**: Operates at both Layer 4 and Layer 7, but is considered legacy. Provides basic load balancing across multiple Amazon EC2 instances and operates at both the request level and connection level.(Application: legacy applications, simple load balancing needs, etc.)
   - **Gateway Load Balancer**: Combines a transparent network gateway and a load balancer to deploy, scale, and manage third-party virtual appliances(to distribute connections across third-party virtual appliances)

8. **What is an Elastic IP?**
   - An Elastic IP address is a static IPv4 address designed for dynamic cloud computing. It allows you to associate a public IP address with your EC2 instance, which can be remapped to another instance in case of failure or maintenance.
   - Elastic IPs are not free, you are changed even if the Elastic IP is not associated with any instance.