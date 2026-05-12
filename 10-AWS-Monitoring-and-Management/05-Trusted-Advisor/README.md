## AWS Trusted Advisor

**AWS Trusted Advisor** is an automated best practice checker — it continuously inspects your AWS environment across **6 categories** and gives recommendations to improve cost, performance, security, fault tolerance, and compliance.

- No setup needed — runs automatically against your account.
- Results visible in the console, or via **API** (Business/Enterprise support plans).
- Integrates with **CloudWatch** — set alarms when a check changes status (green → red).
- Integrates with **EventBridge** — trigger automated remediation when a check fails.

---

## Check Categories

### 1. Cost Optimization
Finds wasted or underutilized resources you're paying for but not using.

- Idle EC2 instances (low CPU utilization).
- Unattached EBS volumes.
- Underutilized RDS instances.
- Reserved Instance purchase recommendations (based on On-Demand usage patterns).
- S3 buckets with no lifecycle policies — data accumulating unnecessarily.

### 2. Performance
Identifies bottlenecks and resource limits that could degrade performance.

- EC2 instances with consistently high CPU — candidate for upsizing or horizontal scaling.
- CloudFront distributions not using edge caching effectively.
- EBS volumes with high I/O wait — candidate for switching to provisioned IOPS (io1/io2).
- Route 53 alias records vs CNAME — alias is faster and free for AWS endpoints.

### 3. Security
Flags security gaps and misconfigurations before attackers exploit them.

- **S3 bucket permissions** — buckets with public read/write access.
- **Security groups** — unrestricted access (0.0.0.0/0) on sensitive ports (22, 3389, etc).
- **MFA on root account** — alerts if root does not have MFA enabled.
- **IAM access key rotation** — keys older than 90 days.
- **CloudTrail not enabled** — no audit trail for API calls.
- **EBS public snapshots** — snapshots accessible to any AWS account.
- **RDS public snapshots** — same issue for databases.

### 4. Fault Tolerance
Checks resilience — whether your architecture can survive failures.

- RDS instances without **Multi-AZ** enabled.
- EC2 instances not spread across multiple **Availability Zones**.
- EBS volumes without recent **snapshots**.
- Auto Scaling groups with insufficient capacity across AZs.
- Route 53 health checks not configured for failover routing.

### 5. Service Limits (Service Quotas)
Warns when you're approaching AWS service quotas — hitting a limit causes failures with no warning.

- EC2 instance limits per region.
- VPC limits (5 VPCs per region by default).
- EIP limits, SG rules limits, Lambda concurrent executions, etc.
- Shows current usage vs limit with a warning at **80%** utilization.

### 6. Operational Excellence *(Business/Enterprise only)*
Checks that operational best practices are followed.

- CloudFormation stacks with drift detected.
- Systems Manager not enabled for EC2 fleet.
- S3 buckets without versioning.

---

## Access by Support Plan

| Check Category | Basic / Developer | Business / Enterprise |
|---|---|---|
| **Core security checks** (~7) | ✓ | ✓ |
| **Service limits** | ✓ | ✓ |
| **All checks (200+)** | ✗ | ✓ |
| **CloudWatch integration** | ✗ | ✓ |
| **Programmatic API access** | ✗ | ✓ |
| **Automated refresh** | Weekly | Every 5 minutes |

---

## Trusted Advisor vs AWS Config vs Security Hub

| | Trusted Advisor | AWS Config | Security Hub |
|---|---|---|---|
| **Focus** | Best practice recommendations across 6 categories | Configuration compliance and drift detection | Centralized security findings aggregator |
| **Real-time** | Near real-time (Business/Enterprise) | On configuration change | Continuous |
| **Remediates** | No — only recommends | Yes — auto-remediation rules | No — aggregates findings |
| **Security checks** | Basic misconfigurations | Config rules against resources | Findings from GuardDuty, Inspector, Macie, Config |
| **Cost checks** | Yes | No | No |
| **Best for** | Quick account health overview | Ongoing compliance and drift | Centralized security posture |
