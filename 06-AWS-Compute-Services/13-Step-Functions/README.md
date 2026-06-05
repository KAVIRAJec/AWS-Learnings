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

---

## AWS SWF (Simple Workflow Service)

AWS SWF is an **older, fully managed workflow orchestration service** for coordinating work across distributed application components — including human tasks and external processes. It predates Step Functions and is largely superseded by it for new workloads.

**Key concepts:**
- **Domain**: A logical container for all workflow and activity resources.
- **Workflow**: Defines the overall business process — implemented as a **Decider** (your code that decides what to do next based on the workflow history).
- **Activity**: A unit of work performed by an **Activity Worker** — can run on EC2, on-premises servers, or any machine that polls SWF.
- **Task**: SWF assigns tasks to workers — **activity tasks** (do some work) or **decision tasks** (decide what to do next).
- **Workflow history**: SWF maintains a full, durable event history for every workflow execution — the decider reads the history to decide the next step.

**Key properties:**
- **Maximum workflow duration: 1 year** — suitable for very long-running business processes.
- Workers **poll** SWF for tasks — SWF assigns tasks and tracks their completion.
- Guarantees **exactly-once task assignment** — a task is assigned to exactly one worker at a time.
- Supports **human tasks** natively — a workflow can wait indefinitely for a human to complete a step (e.g., manual approval, physical fulfillment).
- Runs worker code anywhere — EC2, on-premises, or any internet-connected machine.

**SWF vs Step Functions:**

| | AWS SWF | AWS Step Functions |
|---|---|---|
| **Workflow definition** | Code (Decider logic in your app) | JSON (Amazon States Language — no code) |
| **Worker model** | You write and manage Decider + Activity Workers | AWS manages orchestration — you only write task code |
| **Human tasks** | Native support — waits for human input | Via `waitForTaskToken` callback pattern |
| **Max duration** | 1 year | 1 year (Standard) / 5 min (Express) |
| **Visibility** | No visual workflow graph | Visual state machine graph in console |
| **Recommended for** | Legacy workloads, on-premises worker integration | All new workflow use cases |

> **AWS recommends Step Functions for all new workflows.** SWF is still available and still used in legacy systems — particularly when workers run outside AWS or when complex custom decider logic is required.
