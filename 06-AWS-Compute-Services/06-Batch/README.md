## AWS Batch

**AWS Batch** is a fully managed service that runs **batch computing jobs** at any scale — you submit jobs, Batch handles provisioning, scheduling, queuing, retries, and scaling automatically.

- No infrastructure to manage — runs jobs on **EC2, Spot Instances, or Fargate**.
- Batch ≠ serverless — it provisions compute only when jobs are submitted, then terminates it.
- Jobs are packaged as **Docker containers**.

---

## Key Concepts

- **Job** — the unit of work — a containerized script/application with defined CPU and memory requirements.
- **Job Definition** — a template for a job: Docker image, vCPU, memory, IAM role, environment variables, retry strategy.
- **Job Queue** — jobs are submitted here and wait until compute is available. Multiple queues with different priorities.
- **Compute Environment** — the pool of EC2 / Fargate resources that runs jobs. Can be managed (AWS handles scaling) or unmanaged (you manage EC2 fleet).

```
Submit Job
    │
    ▼
Job Queue (priority-based)
    │
    ▼
Batch Scheduler
    │  picks compute based on job requirements
    ▼
Compute Environment (EC2 / Spot / Fargate)
    │  runs Docker container
    ▼
CloudWatch Logs (output)
```

---

## Managed vs Unmanaged Compute

| | Managed | Unmanaged |
|---|---|---|
| **Scaling** | AWS auto-scales instances | You manage EC2 fleet |
| **Instance selection** | AWS picks optimal type | You specify instance types |
| **Best for** | Most use cases | Custom hardware requirements |

**Fargate compute** — fully serverless, no EC2 instances at all. Best for short jobs with no GPU requirement.
**Spot compute** — uses Spot Instances for up to 90% cost savings; ideal for fault-tolerant batch workloads.

---

## AWS Batch vs Lambda

| | AWS Batch | Lambda |
|---|---|---|
| **Runtime limit** | No limit | 15 minutes max |
| **Container** | Any Docker image | Limited runtimes |
| **Resources** | Up to 96 vCPUs, 720 GB RAM | 10 GB RAM max |
| **Best for** | Long-running, heavy compute jobs | Short event-driven functions |
