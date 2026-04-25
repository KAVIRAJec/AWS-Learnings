## AWS Step Functions

AWS Step Functions is a **serverless graph workflow orchestration service** that coordinates multiple AWS services (Lambda, ECS, SNS, SQS, DynamoDB, etc.) into visual, auditable workflows using state machines.

**Key Concepts:**
- **State Machine**: A workflow defined in **Amazon States Language (ASL)** — JSON-based definition of states and transitions.
- **State**: A single step in the workflow. Types:
   - **Task**: Calls a Lambda function, ECS task, API Gateway, or another AWS service.
   - **Choice**: Branching logic based on conditions.
   - **Wait**: Pauses execution for a set time or until a timestamp.
   - **Parallel**: Runs multiple branches simultaneously.
   - **Map**: Iterates over a list and runs the same steps for each item.
   - **Pass**: Passes input to output without doing work (useful for testing/debugging).
   - **Succeed / Fail**: Terminates the execution successfully or with an error.
- **Execution**: A single run of a state machine with a given input.

**Workflow Types:**

| | Standard Workflow | Express Workflow |
|-|-------------------|-----------------|
| Duration | Up to **1 year** | Up to **5 minutes** |
| Execution model | Exactly-once | At-least-once |
| Pricing | Per state transition | Per execution duration + count |
| Use case | Long-running, auditable workflows | High-volume, short-lived event processing |

**Error Handling:**
- Built-in **retry** and **catch** mechanisms per state — no need to handle retries in application code.

**Integrations:**
- **Optimistic (SDK) Integration**: Step Functions calls services and moves on.
- **Optimistic (Sync) Integration**: Step Functions waits for the service to complete before transitioning.
- **Callback Pattern (waitForTaskToken)**: Pauses execution until an external system sends a task token back — useful for human approval steps.

**Use cases:** Order processing pipelines, data ETL orchestration, human-in-the-loop workflows, ML training pipelines, microservice coordination.
