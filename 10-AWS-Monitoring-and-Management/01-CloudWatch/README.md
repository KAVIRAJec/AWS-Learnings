## Amazon CloudWatch

Amazon CloudWatch is AWS's **monitoring and observability service** — collects metrics, logs, and events from AWS resources and applications, then lets you visualize, alarm, and act on that data.

---

## Metrics

**Key Concepts:**
- **Metric**: A time-series of data points representing a measurable value (e.g., EC2 CPU utilization, Lambda duration, RDS connections).
- **Namespace**: A container for metrics — e.g., `AWS/EC2`, `AWS/Lambda`. Custom metrics go in your own namespace.
- **Dimension**: A key-value pair that identifies a metric (e.g., `InstanceId=i-1234abcd`). Up to 30 dimensions per metric.
- **Resolution**:
  - **Standard** (60s granularity) — default for most AWS services.
  - **High-Resolution** (1s granularity) — for custom metrics needing sub-minute precision.
- **Metric Retention**:
  - < 60s resolution → retained **3 hours**
  - 60s resolution → retained **15 days**
  - 300s resolution → retained **63 days**
  - 3600s resolution → retained **15 months**

**Default vs Custom Metrics:**
- **Default**: AWS services publish metrics automatically — CPU, network, disk I/O for EC2, invocation count and errors for Lambda, etc. **Memory and disk usage on EC2 are NOT included** — need CloudWatch Agent.
- **Custom**: Published via `PutMetricData` API from your application — latency, order count, queue depth, etc.

---

## Alarms

- **Alarm States**: `OK` → metric within threshold. `ALARM` → threshold breached. `INSUFFICIENT_DATA` → not enough data yet.
- **Actions on ALARM**: Notify via **SNS**, trigger **Auto Scaling** (scale in/out), perform **EC2 actions** (stop, reboot, terminate, recover).
- **Evaluation Period**: Number of consecutive periods the metric must breach the threshold before alarm triggers — reduces false positives.
- **Composite Alarms**: Combine multiple alarms using AND/OR logic — alarm only when several conditions are true simultaneously. Reduces alert noise.
- **Alarm on Math Expression**: Perform math across multiple metrics (e.g., error rate = errors / requests × 100) and alarm on the result.

**EC2 Instance Recovery:**
- Alarm on `StatusCheckFailed_System` metric (monitors underlying hardware health) → trigger **Recover** action.
- Recovery migrates the instance to healthy hardware — retains the same **Instance ID, private IP, Elastic IP, and EBS volumes**. In-memory data (RAM) is lost.
- Alarm on `StatusCheckFailed_Instance` metric (monitors OS/software health) → trigger **Reboot** action to recover from OS-level issues.
- Recovery notification can be sent via **SNS** to alert the team when an instance is auto-recovered.

---

## Logs

**Key Concepts:**
- **Log Group**: Container for logs from a specific source/application — define retention and metric filters at this level.
- **Log Stream**: Sequence of log events from a single source (one EC2 instance, one Lambda execution environment).
- **Retention**: Default is **Never Expire**. Set per log group: 1 day to 10 years. Logs beyond retention are deleted automatically.
- **CloudWatch Logs Insights**: Interactive query language to search and analyze log data — `fields`, `filter`, `stats`, `sort`, `limit`. Results shown as tables or time-series charts.
- **Metric Filters**: Extract a numeric value from log lines and publish it as a CloudWatch metric — e.g., count ERROR occurrences → alarm on error rate.
- **Subscription Filter**: Stream log events in real time to **Kinesis Data Streams**, **Kinesis Firehose**, or **Lambda** for processing or delivery to other destinations (OpenSearch, S3).

**Common Log Sources:**
- EC2 instances (via **CloudWatch Agent**)
- Lambda (automatic — stdout/stderr go to `/aws/lambda/<function-name>`)
- CloudTrail, VPC Flow Logs, Route 53, API Gateway, RDS

**Log Classes:**

| | Standard | Infrequent Access (IA) |
|---|---|---|
| **Best for** | Actively monitored logs | Infrequently queried logs (audit, compliance, archives) |
| **Ingestion cost** | Higher | ~50% lower |
| **Features** | Full — Insights, Metric Filters, Subscriptions, Alarms | Insights queries only — no Metric Filters, Subscriptions, or Alarms |
| **Retention** | 1 day – 10 years | 1 day – 10 years |
| **Switch** | Can convert Standard → IA anytime (not reversible per log group) | — |

- Set at the **log group** level — all streams in the group inherit the class.
- Use **IA** for logs you need to keep but rarely query — reduces storage cost significantly without deleting data.

**Live Tail:**
- Stream log events in **real time** directly in the console or CLI — see logs as they are ingested, no delay.
- Filter by log group, log stream, or a pattern — only matching events are shown.
- Useful for active debugging — watch Lambda errors, API responses, or container logs live during a deployment or incident.
- **Pricing**: Charged per GB of log data scanned during the Live Tail session — not per time spent viewing. Stopping the session stops the charge.
- Available for **Standard class** log groups only — not supported on Infrequent Access.

**Exporting Logs:**
- Cloudwatch logs can be exported to S3 for long-term storage or to OpenSearch for advanced search and visualization.
- Log data can take upto 12 hours to be available for export. To export logs, use the `CreateExportTask` API or the console — specify log group, time range, and S3 bucket.
- CloudWatch Logs from multiple accounts or regions can be aggregated into a single view for centralized monitoring.
---

## CloudWatch Agent

Two generations of agents — **Logs Agent** (legacy) and **Unified Agent** (current):

| | Logs Agent (Legacy) | Unified Agent (Current) |
|---|---|---|
| **Purpose** | Logs only | Logs + system-level metrics |
| **Metrics** | None | Memory, disk, swap, CPU, processes |
| **Config** | Per-instance config file | Centralized via **SSM Parameter Store** |
| **Platform** | Linux only | Windows and Linux |
| **Status** | Deprecated — no new features | Recommended for all new setups |

**Unified Agent — what it unlocks:**
- **Metrics**: Memory utilization, disk usage, swap, number of processes — all missing from EC2 default metrics.
- **Logs**: Any log file on the filesystem — application logs, system logs, custom paths — sent to a specified log group.
- Config stored in **SSM Parameter Store** — deploy and update agent config centrally across a fleet without touching each instance.
- Requires an **IAM role** attached to the EC2 instance with permissions to publish metrics and logs to CloudWatch.

---

## Dashboards

- Customizable visual panels displaying metrics, alarms, and log query results.
- **Cross-region and cross-account**: A single dashboard can show metrics from multiple AWS regions and accounts.
- **Automatic dashboards**: Pre-built per service (EC2, Lambda, RDS) — available out of the box with no setup.
- Dashboards are **global** — accessible regardless of selected region in the console.

---

## CloudWatch Insights (Advanced)

**Container Insights:**

Collect, aggregate, and summarize metrics and logs from **ECS, EKS, and self-managed Kubernetes on EC2** — gives visibility at the cluster, node, pod, and container level without custom instrumentation.

- **Metrics collected**: CPU, memory, disk, network per cluster/node/pod/container/task — published to CloudWatch as custom metrics under the `ContainerInsights` namespace.
- **Logs collected**: Application logs from containers, Kubernetes control plane logs (API server, scheduler, controller manager) — shipped to CloudWatch Logs automatically.
- **Performance dashboards**: Pre-built dashboards in the CloudWatch console show resource utilization per cluster, service, and pod — no manual setup required.
- **Alarms**: Set alarms on any Container Insights metric — e.g., alert when pod memory utilization exceeds 80%.
- **Diagnostic data**: Captures container failure reasons, OOMKilled events, and crashed pod details — surfaced alongside metrics for faster root cause analysis.

**Setup:**
- **ECS**: Enable Container Insights at the cluster level (one toggle in the console or via CLI `--settings`).
- **EKS / Kubernetes**: Deploy the **CloudWatch Observability agent** (or the older CloudWatch Agent + Fluent Bit DaemonSet) to each node — agent collects metrics, Fluent Bit ships logs.

**ECS vs EKS:**

| | ECS Container Insights | EKS Container Insights |
|---|---|---|
| **Setup** | Cluster-level toggle | Agent DaemonSet on each node |
| **Granularity** | Cluster, Service, Task, Container | Cluster, Node, Pod, Container |
| **Log shipping** | Via FireLens or CloudWatch Logs driver | Via Fluent Bit DaemonSet |
| **Cost** | Custom metrics + log ingestion charges | Custom metrics + log ingestion charges |

**Lambda Insights:**
- Detailed Lambda performance metrics — cold starts, duration distribution, memory usage, init duration.
- Deployed as a Lambda Layer — no code changes required.

**Contributor Insights:**
- Analyze log data to identify **top-N contributors** to a metric — e.g., which IP addresses are making the most requests, which user IDs are generating the most errors.
- Useful for finding noisy neighbours and traffic anomalies.

**Synthetics (Canaries):**
- Scheduled scripts (Node.js or Python) that mimic user actions — test URLs, APIs, and workflows on a schedule.
- Alerts if availability or latency degrades **before real users are impacted**.
- Stores screenshots and HAR files in S3 for debugging.

**Application Insights:**
- Automatically detects and monitors the **key components and dependencies** of your application — sets up metrics, logs, and alarms without manual configuration.
- **Application**: Defined by a **Resource Group** (e.g., all resources tagged `env=prod`) — Application Insights auto-discovers all components (EC2, RDS, ELB, Lambda, SQS) within it.
- **Problem**: Correlates related metrics, logs, and events into a single problem record with an ML-generated root cause summary — instead of flooding you with individual alarms. **SageMaker models** analyze the data to identify the most likely cause and surface relevant logs and metrics for troubleshooting.
- **Insight**: ML-generated explanation linking the likely cause to relevant logs and metrics (e.g., "high CPU on EC2 correlated with slow DB query logs").
- Monitors automatically: response time, error rates, CPU/memory (via agent), log anomalies, RDS slow queries, and .NET/SQL Server workloads.
- **Integrations**: Problems auto-created as **OpsCenter OpsItems** for tracking; published to **EventBridge** for notifications; correlated with **X-Ray** traces for end-to-end visibility.

---

## CloudWatch Network Synthetic Monitor

CloudWatch Network Synthetic Monitor (NSM) is a **network reachability and performance monitoring** service — continuously tests network paths from your VPC resources to endpoints and surfaces where latency or packet loss is occurring, down to the network hop level. It works VPC, Transit Gateway, Direct Connect(On-premises), and internet paths.

**Key Concepts:**
- **Monitor**: The core resource — defines the source (your VPC subnet/ENI) and the destination (IP, hostname, or AWS service endpoint) to test.
- **Probe**: A continuous synthetic test sent from the source to the destination — measures **round-trip latency** and **packet loss** at regular intervals.
- **Network Path**: NSM traces the actual network hops between source and destination — identifies exactly which segment (VPC, Transit Gateway, internet) is introducing latency or drops.
- Works entirely within AWS network telemetry — no agents or software to install on instances.

**What it Monitors:**
- VPC-to-VPC connectivity (peering, Transit Gateway paths)
- VPC-to-on-premises paths (Direct Connect, VPN)
- VPC-to-internet or VPC-to-AWS service endpoints
- Multi-hop path visibility — pinpoints the specific hop causing degradation

**How it Differs from Synthetics (Canaries):**

| | Synthetics (Canaries) | Network Synthetic Monitor |
|---|---|---|
| **Layer** | Application (L7) — HTTP/S | Network (L3/L4) — ICMP/TCP |
| **Tests** | URL availability, API responses, user workflows | Reachability, latency, packet loss per network hop |
| **Setup** | Write scripts (Node.js/Python) | No code — configure source/destination |
| **Use case** | App uptime and UX monitoring | Network path troubleshooting |

**Use cases:** Diagnosing intermittent packet loss between VPCs, monitoring Direct Connect or VPN path latency, proactively detecting network degradation before users report issues, validating network changes after infrastructure updates.

---