## AWS Lambda

AWS Lambda is a serverless compute service that lets you run code without provisioning or managing servers. It automatically scales and executes your code in response to events. You simply upload your code (functions) to Lambda, and the service automatically handles everything required to run and scale the execution

### Features
- **Event-driven**: Automatically runs your code in response to events.
- **Scalable**: Handles any scale of requests.
- **Flexible**: Supports multiple programming languages (Python, Node.js, Java, .NET(C#), Go, Ruby etc.) and can be triggered by various AWS services.

### Use Cases
- **Data Processing**: Process data in real-time like managing EC2 instances, processing files in S3.
- **Automation**: Automate tasks like backups, monitoring, and notifications.


### Pricing
- **Pay-per-use**: You are charged based on the number of requests and the duration of your code execution. The first 1 million requests per month are free, and you pay for the compute time used after that. You pay for the compute time based on how long your code runs.

### Interview Questions
1. **What are the benefits of using AWS Lambda?**
   - No server management
   - Automatic scaling
   - Cost-effective
   - Event-driven architecture.

2. **What is the maximum execution time for a Lambda function?**
   - The maximum execution time for a Lambda function is 15 minutes. If your function takes longer than that, it will be terminated automatically.

3. **How does AWS Lambda handle concurrency?**
   - AWS Lambda automatically scales the number of instances based on the number of incoming requests. You can set concurrency limits to control the maximum number of concurrent executions.

4. **Explain Lambda execution environment.**
   - The Lambda execution environment is a secure and isolated environment where your code runs. It includes the runtime, networking, temporary storage (/tmp directory) and any dependencies required to execute your code.
   - Each Lambda function runs in its own execution environment, which is created when the function is invoked for the first time and reused for subsequent invocations.

5. **Does Lambda Function access other AWS services?**
   - Yes, Lambda functions can access other AWS services such as S3, DynamoDB, and SNS, provided the necessary permissions are granted through IAM roles. The IAM role associated with the Lambda function must have the required permissions to access the resources.

6. **Limitations of AWS Lambda?**
   - Maximum execution time of 15 minutes.
   - Limited temporary storage of 512MB (in the /tmp directory).
   - AWS Lambda may not be ideal for long-running or resource-heavy applications.
   - Limited to 1000 concurrent executions by default.

7. **API Gateway in Lambda?**
   - API Gateway is commonly used to expose AWS Lambda functions as HTTP endpoints. It acts as the front-end that clients use to trigger Lambda functions via RESTful or WebSocket APIs. 
   - API Gateway provides features like rate limiting, authorization, and request validation, which can be useful when exposing Lambda functions to the public or internal clients.

8. **Cold start in Lambda?**
   - A cold start occurs when a Lambda function is invoked for the first time or after a period of inactivity. During a cold start, AWS Lambda must create a new execution environment, which can lead to increased latency for the first request.
   - Subsequent invocations of the same function within a short period will use the existing execution environment, resulting in lower latency.