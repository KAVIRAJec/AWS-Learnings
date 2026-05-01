## Amazon EventBridge (formerly CloudWatch Events)

Amazon EventBridge is a **serverless event bus** that routes events from AWS services, custom applications, and SaaS providers to target services — enabling event-driven architectures.

**Key Concepts:**
- **Event**: A JSON message describing a state change (e.g., EC2 instance stopped, S3 object uploaded, custom app event).
- **Schema**: The structure of an event, defined by its source and detail type. It can versioned and stored in the Schema Registry.
- **Event Bus**: A channel that receives events. Types:
   - **Default Event Bus**: Receives events from AWS services automatically.
   - **Custom Event Bus**: For events from your own applications.
   - **Partner Event Bus**: For events from SaaS providers (e.g., Datadog, Zendesk, Auth0).
- **Rule**: Matches incoming events against a pattern and routes them to one or more targets.
- **Target**: The destination that processes the event — Lambda, SQS, SNS, Step Functions, ECS task, Kinesis, API Gateway, and more.
- **Event Pattern**: JSON filter that defines which events a rule matches (e.g., all EC2 `stopped` state changes in `us-east-1`).
- **Schedule**: Rules can also trigger on a **cron or rate schedule** (replaces CloudWatch Scheduled Events).
- **Cross-account & Cross-region events**: EventBridge can route events across AWS accounts and regions using resource-based policies.

**EventBridge vs SNS:**
- EventBridge: Content-based filtering, schema registry, SaaS integrations, more targets — best for event-driven architecture.
- SNS: Simpler pub/sub, faster, better for high-throughput fan-out notifications.

**Schema Registry:**
- Automatically discovers and stores the schema of events flowing through EventBridge.
- Generate code bindings for your preferred language to work with event schemas.

**Archive & Replay:**
- **Archive**: Store all events (or a filtered subset) published to an event bus indefinitely or for a set retention period.
   - Configured per event bus — define an event pattern to archive only matching events (e.g., only `order.*` events), or archive everything.
   - **Retention**: Indefinite or a specific number of days. Events beyond retention are deleted automatically.
   - Archived events are stored internally by EventBridge — no S3 bucket required.
- **Replay**: Re-send archived events back to the event bus as if they occurred now — targets process them through existing rules.
   - Specify a time window (start/end) to replay only events from that period.
   - Use cases: recover from a bug that caused events to be misprocessed, onboard a new target/consumer against historical events, test a new rule against real past traffic.
   - Replay can be **paused and resumed** — useful for large event windows.

**Use cases:** Reacting to AWS service events, scheduled tasks (cron jobs), SaaS integrations, decoupling microservices via events, replaying past events after a bug fix or new consumer onboarding.
