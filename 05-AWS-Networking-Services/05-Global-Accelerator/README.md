## Global Accelerator

- AWS Global Accelerator is a networking service that improves the  availability and performance of your applications for global users. 
- It provides static IP addresses that act as a fixed entry point to your application endpoints in one or more AWS Regions, such as Application Load Balancers, Network Load Balancers, and Amazon EC2 instances.
- It uses the AWS global network to route traffic to the optimal endpoint based on health, geography, and latency, providing a consistent and fast user experience.

**Key Features:**
1. **Static IP Addresses**: Global Accelerator provides two static IP addresses that act as a fixed entry point to your application, improving availability and reducing latency.
2. **Health Checks**: It continuously monitors the health of your application endpoints and routes traffic to healthy endpoints, ensuring high availability.
3. **Traffic Routing**: Global Accelerator uses the AWS global network to route traffic to the optimal endpoint based on health, geography, and latency, improving performance for users.
4. **DDoS Protection**: Global Accelerator provides built-in DDoS protection, helping to safeguard your applications from attacks.
5. **Multi-Region Support**: It allows you to route traffic to multiple AWS Regions, improving availability and performance for global users.

**Use Cases:**
- **Global Applications**: Global Accelerator is ideal for applications with a global user base, such as gaming, media streaming, and e-commerce applications.
- **Disaster Recovery**: It can be used to route traffic to backup endpoints in different AWS Regions, improving availability and resilience.
- **Latency Optimization**: Global Accelerator can help reduce latency for applications with users in different geographic locations by routing traffic to the nearest endpoint.
