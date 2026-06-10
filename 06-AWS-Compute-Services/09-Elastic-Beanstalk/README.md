## AWS Elastic Beanstalk

- AWS Elastic Beanstalk is a Platform as a Service (PaaS),fully managed service that simplifies the deployment and scaling of web applications and services. 
- It supports various programming languages, including Java, .NET, PHP, Node.js, Python, Ruby, Go, and Docker. 
- Elastic Beanstalk abstracts the infrastructure layer, allowing developers to focus on writing code without managing the underlying hardware.
- Internally, the resources are automatically provisioned using AWS CloudFormation, which is a service that allows you to create and manage AWS resources using templates. 
- You are only charged for the underlying resources you use and no separate charges for Elastic Beanstalk, and you can scale your application up or down based on demand.
- **Load balancing and auto-scaling** are automatically configured for you.
- With basic health reporting, Elastic beanstalk doesn't publish logs to CloudWatch.


## Key Concepts
- **Application**: A logical collection of Elastic Beanstalk components, including environments, versions, and configurations.
- **Environment**: A version of the application running on AWS resources (e.g., EC2, RDS, S3). Each environment can be configured independently.
- **Environment Tier:**
    - **Web Server Environment**: For web applications that handle HTTP requests and responses. It typically includes a load balancer, EC2 instances, and an auto-scaling group.
    - **Worker Environment**: For background processing tasks that do not require user interaction. It typically includes EC2 instances that process messages from an Amazon SQS queue.

## File and Log Storage

- **Application files**: Stored in **Amazon S3**. When you upload your application via the console, CLI, or IDE toolkit, Elastic Beanstalk automatically copies the files to an S3 bucket in your account.
- **Server log files**: Stored on the **EBS volumes of the EC2 instances** by default. Optionally can be streamed or archived to:
  - **Amazon S3** — Elastic Beanstalk can be configured to copy server logs to S3 every hour via environment configuration settings.
  - **Amazon CloudWatch Logs** — the CloudWatch Logs agent on each EC2 instance publishes log streams; configure alarms on metric filters for specific log events.
- Logs cannot be stored directly to **Glacier** (must go through S3 lifecycle policy) or **CloudTrail** (CloudTrail is for API audit logs, not application logs).

## Workflow
1. **Upload Code:** Developers upload their application code to Elastic Beanstalk.(.zip or docker image)
2. **Provision Resources:** Elastic Beanstalk automatically provisions resources like EC2, ELB, and Auto Scaling.
3. **Deploy Application:** The application is deployed to the provisioned environment.
4. **Monitor and Scale:** Elastic Beanstalk monitors the application and automatically scales resources based on demand.
