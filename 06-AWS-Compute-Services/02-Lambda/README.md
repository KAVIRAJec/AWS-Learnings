## AWS Lambda

AWS Lambda is a **serverless compute service** that runs code in response to events — no servers to provision, manage, or scale. You only pay for the compute time your code actually uses.

**Key Concepts:**
- **Function**: Your code + runtime + configuration. Supports Python, Node.js, Java, .NET, Go, Ruby, and custom runtimes.
- **Trigger**: An event source that invokes the function — S3, API Gateway, DynamoDB Streams, SQS, SNS, CloudWatch Events, EventBridge etc.
- **Execution Role**: IAM role attached to the function that grants it permissions to access AWS services.
- **Timeout**: Max execution time per invocation — up to **15 minutes** (default 3s).
- **Memory**: Configurable from **128 MB to 10 GB**. CPU scales proportionally with memory. Increasing memory can improve CPU performance.
- **Ephemeral Storage**: `/tmp` directory — up to **10 GB** for temporary files during execution.
- **Environment Variables**: Key-value pairs injected into the function at runtime. Can be encrypted with KMS. Upto 4KB only.

**Invocation Types:**
- **Synchronous**: Caller waits for the result (e.g., API Gateway, SDK direct call).
- **Asynchronous**: Lambda queues the event and processes it — caller doesn't wait (e.g., S3 events, SNS). Failed events can go to a **Dead Letter Queue (DLQ)** or **EventBridge**.
- **Event Source Mapping**: Lambda polls a source and processes records in batches (e.g., SQS, Kinesis, DynamoDB Streams).

**Scaling & Concurrency:**
- Lambda scales automatically — each invocation runs in its own isolated environment.
- **Concurrency**: Number of functions running simultaneously. Default limit: 1000 per region (can be increased by requesting a limit increase). This limit is applied across all functions in the account per region.
- **Reserved Concurrency**: Guarantee a set number of concurrent executions for a function. Set at a function level to ensure it always has capacity or to limit it from consuming all concurrency. Beyond reserved concurrency, the function will be throttled.(249: ThrottleError(Synchronous Execution)(Retry automatically upto 6 hrs(retry interval increase exponentially upto 5mins) & then go to DLQ if still fails(Async Invocation)))
- **Provisioned Concurrency**: Concurrency is allocated in advance before the function is invoked, this eliminates cold starts with low latency. This works when application has large dependencies.

**Deployment:**
- Compressed ZIP file deployment size limit: 50 MB (direct upload)
- Uncompressed deployment size limit: 250 MB (Code + Dependencies).

**Layers:**
- Package shared libraries or dependencies as a **Layer** — reuse across multiple functions without bundling in each deployment package.
- Up to **5 layers per function**.
- **Size limit**: Each layer is capped at **250 MB unzipped**. The total unzipped size of the function code + all layers combined must stay within the **250 MB deployment limit**.
- **Immutable once published** — a layer version cannot be edited or overwritten. Any change requires publishing a **new version** with a new version number. Functions must be updated to point to the new version explicitly.

**Lambda in VPC:**
- By default, Lambda runs outside your VPC. Configure it to run inside a VPC to access private resources (RDS, ElastiCache).
- When placed in a VPC, Lambda uses an **ENI** — needs appropriate subnet and security group.

**Lambda Execution Lifecycle (3 Phases):**
- **Init Phase**: Lambda initializes the execution environment — downloads the code, starts the runtime, and runs the initialization code outside the handler (e.g., DB connections, SDK clients). Happens on **cold start** only.
- **Invoke Phase**: Lambda runs the handler function with the event input. This happens on every invocation (cold and warm).
- **Shutdown Phase**: Lambda shuts down the runtime and execution environment after a period of inactivity. Any cleanup code in extensions runs here.

**Cold Start vs Warm Start:**
- **Cold start**: Init phase runs → slower first response. Happens when a new execution environment is created (new deployment, scaling up, or after idle).
- **Warm start**: Execution environment reused → Init phase skipped → faster response.

**Lambda SnapStart:**
- A feature for **Java runtimes** that reduces cold start latency significantly.
- When you publish a new version, Lambda takes a **snapshot** of the initialized execution environment (after Init phase) — caches the memory and disk state.
- On invocation, Lambda **restores from the snapshot** instead of running Init again → dramatically faster cold starts (up to 10x).
- Enabled per function version — only supports **Java 11+ (Corretto)** runtime.
- **Uniqueness hooks**: Since multiple environments restore from the same snapshot, use `beforeCheckpoint` / `afterRestore` lifecycle hooks to re-initialize anything that must be unique per instance (e.g., random seeds, network connections).

**Pricing:**
- **Requests**: First 1M/month free(free tier), then $0.20 per 1M requests.
- **Duration**: 400,000 GB-seconds per month free(free tier), then $0.00001667 per GB-second.

**Customization at the Edge:**
Run code closer to users at **CloudFront edge locations** — without routing requests all the way to the origin. Two options:

- **CloudFront Functions**:
   - Lightweight JavaScript functions running at **all 400+ CloudFront edge locations**.
   - Sub-millisecond startup, millions of requests/second.
   - Limited to: **Viewer Request** and **Viewer Response** events only.
   - Use cases: URL rewrites/redirects, HTTP header manipulation, simple auth (JWT validation), A/B testing.
   - Max execution time: **<1ms**. No network/file system access.

- **Lambda@Edge**:
   - Full Lambda functions (Node.js or Python) deployed to **regional edge locations** (~13 regions).
   - Supports all 4 CloudFront events:
      - **Viewer Request**: Before CloudFront checks cache.
      - **Origin Request**: Before request is forwarded to origin (cache miss).
      - **Origin Response**: After origin responds, before CloudFront caches it.
      - **Viewer Response**: Before response is sent to the user.
   - Max execution time: **5s** (viewer events), **30s** (origin events).
   - Supports network calls, file system access, and larger compute.
   - Use cases: Dynamic content rendering, user authentication, bot detection, image transformation at the edge.

| | CloudFront Functions | Lambda@Edge |
|-|---------------------|-------------|
| Runtime | JavaScript only | Node.js, Python |
| Execution time | < 1ms | Up to 30s |
| Request limit | Millions/sec | 1000/sec per region |
| Max memory | 2 MB | 128 MB to 10GB |
| Events supported | Viewer req/res only | All 4 events |
| Scale | 400+ edge locations | ~13 regional edges |
| Use case | Simple transforms | Complex logic, network calls |

**Use Cases:**
- API Gateway: Build serverless APIs and invoke Lambda functions on HTTP requests.
- Kinesis: Process data for transformation, filtering, and aggregation.
- DynamoDB: Trigger Lambda on item changes for real-time processing.
- S3: Run code in response to object uploads, deletions, or modifications based on events.
- CloudFront: Lambda@Edge for running functions at AWS edge locations for low-latency responses.
- EventBridge: Build event-driven architectures by routing events to Lambda functions.
- CloudWatch Events: Schedule Lambda functions to run at specific times or intervals (cron jobs).
- SNS: Send notifications or trigger workflows based on messages published to SNS topics.
- SQS: Process messages from SQS queues asynchronously with Lambda.
- Cognito: Use Lambda triggers for custom authentication flows, pre/post authentication, and user migration.

**RDS & Lambda Integration:**
- **Invoking Lambda from RDS/Aurora**: RDS (PostgreSQL, MySQL) and Aurora can invoke a Lambda function directly from within a stored procedure or trigger using `lambda_sync` / `lambda_async` functions — useful for sending notifications or triggering workflows on DB events. This passes data from the DB to Lambda as the event payload.
   - The DB instance must have an **outbound internet access** (via NAT Gateway if in a private subnet) or a **VPC endpoint for Lambda**.
   - The DB instance needs an **IAM role** with `lambda:InvokeFunction` permission.
- **RDS Event Notifications**: RDS publishes DB instance events (failover, backup, maintenance) to **SNS** — SNS can then fan out to Lambda. This is different from invoking Lambda on data changes (use stored procedures for that). No data is passed to Lambda, just event metadata.
- **Aurora Native Invocation**: Aurora MySQL and PostgreSQL support native Lambda invocation via `aurora_invoke_lambda()` — allows calling Lambda directly from SQL queries.