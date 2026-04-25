## Amazon EventBridge

Amazon EventBridge is a **serverless event bus** that routes events from AWS services, custom applications, and SaaS providers to target services — enabling event-driven architectures.

**Key Concepts:**
- **Event**: A JSON message describing a state change (e.g., EC2 instance stopped, S3 object uploaded, custom app event).
- **Event Bus**: A channel that receives events. Types:
   - **Default Event Bus**: Receives events from AWS services automatically.
   - **Custom Event Bus**: For events from your own applications.
   - **Partner Event Bus**: For events from SaaS providers (e.g., Datadog, Zendesk, Auth0).
- **Rule**: Matches incoming events against a pattern and routes them to one or more targets.
- **Target**: The destination that processes the event — Lambda, SQS, SNS, Step Functions, ECS task, Kinesis, API Gateway, and more.
- **Event Pattern**: JSON filter that defines which events a rule matches (e.g., all EC2 `stopped` state changes in `us-east-1`).
- **Schedule**: Rules can also trigger on a **cron or rate schedule** (replaces CloudWatch Scheduled Events).

**EventBridge vs SNS:**
- EventBridge: Content-based filtering, schema registry, SaaS integrations, more targets — best for event-driven architecture.
- SNS: Simpler pub/sub, faster, better for high-throughput fan-out notifications.

**Schema Registry:**
- Automatically discovers and stores the schema of events flowing through EventBridge.
- Generate code bindings for your preferred language to work with event schemas.

**Use cases:** Reacting to AWS service events, scheduled tasks (cron jobs), SaaS integrations, decoupling microservices via events.
