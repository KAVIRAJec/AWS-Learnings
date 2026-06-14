# CLAUDE.md — AWS Learnings Repo Context

## What This Repo Is

Personal AWS study notes for the **SAA-C03 (Solutions Architect Associate)** exam. These are personal learning notes — not an exam guide, not a tutorial. The goal is clean, dense reference material that covers the *why* and the *gotchas*, not just surface definitions.

---

## How We Work Together

The primary workflow is:
1. User pastes a practice question or describes an AWS concept.
2. Check whether the relevant file already covers it.
3. If covered fully → confirm, nothing to add.
4. If missing or only a one-liner → add the specific missing content to the correct file.
5. Never over-engineer. A missing fact gets one bullet or a short section — not a full rewrite.

**Checking before adding**: Always `grep` the relevant file before concluding something is missing. Content may exist in a slightly different section.

---

## Repository Structure

```
AWS-Learnings/
├── 01-Cloud-Concepts/
├── 02-AWS-Global-Infrastructure/       ← Global/Regional/AZ service scope table
├── 03-Cloud-Architecture/              ← Well-Architected Framework, DR strategies
├── 04-AWS-Identity-and-Access-Management/  ← IAM, STS, Cognito, Organizations
├── 05-AWS-Networking-Services/         ← VPC, Route 53, Direct Connect, CloudFront,
│                                          Global Accelerator, Transit Gateway,
│                                          PrivateLink, VPN
├── 06-AWS-Compute-Services/            ← EC2 (with sub-files), Lambda, ECS, ECR,
│                                          EKS, Fargate, Elastic Beanstalk, App Runner,
│                                          API Gateway, Step Functions, AppSync
├── 07-AWS-Storage-Services/            ← S3 (with sub-files), EBS, EFS, FSx,
│                                          AWS Backup, Storage Gateway, DataSync,
│                                          Snow Family, Transfer Family
├── 08-AWS-Database-Services/           ← RDS, DynamoDB, Aurora, Redshift,
│                                          ElastiCache, Neptune, DocumentDB,
│                                          Timestream, Keyspaces, QLDB, DMS
├── 09-AWS-Cost-Optimization/
├── 10-AWS-Monitoring-and-Management/   ← CloudWatch (X-Ray, ServiceLens, App Insights),
│                                          CloudTrail, Config, Systems Manager,
│                                          Trusted Advisor, CloudFormation,
│                                          KMS, Secrets Manager, EventBridge, SQS, SNS,
│                                          Amazon MQ, OpsWorks, Outposts, Cognito,
│                                          CloudHSM, SES, Pinpoint
├── 11-AWS-CICD/
├── 12-AWS-IaC-Tools/
├── 13-Data-Analytics-Services/         ← Athena, Glue, Redshift, Kinesis,
│                                          QuickSight, EMR, OpenSearch, Lake Formation,
│                                          MSK, Data Pipeline, AppFlow
├── 14-Machine-Learning-Services/
├── 15-AWS-Security-Services/           ← ACM, WAF, Shield, Firewall Manager,
│                                          GuardDuty, Inspector, Security Hub,
│                                          Macie, Network Firewall, Artifact
└── 16-AWS-Migration-Services/
```

### EC2 Sub-files (`06-AWS-Compute-Services/01-EC2/`)
| File | Content |
|---|---|
| `README.md` | Overview, instance types |
| `01-Load-Balancing.md` | ALB/NLB/GLB, target types, weighted target groups, slow start mode, access logs, SNI, Cognito auth |
| `02-Auto-Scaling.md` | ASG, scaling policies, cooldown, instance warm-up, lifecycle hooks |
| `03-Launch-Template.md` | Launch templates, version immutability, ASG integration |
| `04-Pricing.md` | On-Demand, Reserved (Zonal vs Regional), Spot, Savings Plans, Capacity Reservations, Dedicated Hosts, vCPU limits |
| `05-Placement-Groups.md` | Cluster, Spread, Partition; insufficient capacity error fix |
| `06-Networking.md` | ENI (primary vs secondary), EFA, enhanced networking |
| `07-Storage-and-AMI.md` | Instance store, EBS, AMI, stop/start behavior, hibernation |

### S3 Sub-files (`07-AWS-Storage-Services/01-S3/`)
| File | Content |
|---|---|
| `README.md` | Buckets, static website hosting, S3 Select, Object Lambda, private by default |
| `01-Storage-Classes.md` | Standard, IA, One Zone-IA, Glacier (Instant/Flexible/Deep Archive), Intelligent-Tiering; Expedited + Provisioned Capacity |
| `02-Replication.md` | CRR, SRR, RTC, Batch Replication; versioning prerequisite |
| `03-Performance.md` | Multipart upload, Transfer Acceleration, byte-range fetches |
| `04-Security.md` | Encryption (SSE-S3/KMS/DKMS/C), CORS, MFA Delete, Access Logs, Presigned URLs, Glacier Vault Lock, Object Lock, Access Points, Object Lambda |

---

## Writing Style Rules

**Always use the current preferred style** (modelled on sections 10–14):

- Lead with a 1–2 sentence service definition.
- `**Key Concepts:**` for core vocabulary bullets.
- Bullet format: `- **Term**: explanation — include real numbers/limits`.
- `---` horizontal rules between major sections.
- Tables for all two-way comparisons (never prose comparison).
- ASCII diagrams for flows and architectures.
- `**X vs Y:**` block at the end when a service is commonly confused with another.
- No trailing summaries. No "key exam points" sections. No exam-focused labels.
- No step-by-step console walkthroughs.
- Pack real numbers: default values, limits, timeouts, retention periods, throughput caps.

**Comments/tone**: Write for someone who knows AWS exists and wants the dense facts — not an intro audience.

---

## Content Rules (What NOT to Do)

- **No "Key exam points (SAA-C03)" sections** — these are personal study notes.
- **No verbose filler** — every sentence earns its place.
- **No duplicate content** — check the file before adding.
- **No premature abstractions** — add only what the specific gap requires.
- **No CloudFormation/CDK code** unless the topic IS IaC.
- **No step-by-step console walkthroughs**.

---

## Key Topics Added in Recent Sessions

A non-exhaustive list of specific content added (useful for knowing what's already covered):

### Compute
- EC2 default vs custom CloudWatch metrics (memory/disk require CloudWatch Agent)
- API Gateway canary release deployment (traffic splitting within one stage)
- ALB Weighted Target Groups (ALB-only, not NLB); ALB target types (instance/ip/lambda)
- ALB Access Logs (disabled by default, delivers to S3 every 5 min, client IP + latency)
- ALB Slow Start Mode (30–900s ramp-up, Round Robin only)
- EC2 ENI primary (non-detachable) vs secondary (detachable for failover)
- EC2 stop/start behavior: instance store lost, host may change, EIP retained, public IP lost
- EC2 hibernation: must enable at launch, RAM saved to EBS, billing continues
- EC2 Instance Lifecycle billing (stopping-to-hibernate billed, Reserved on terminated billed)
- EC2 vCPU-based On-Demand Instance limits (per region, not per AZ)
- Launch Template version immutability (cannot edit, create new version for new AMI)
- ASG Instance Warm-Up vs Scaling Cooldown distinction
- Cluster Placement Group insufficient capacity fix (stop all → restart)
- ECS Auto Scaling metrics: `ECSServiceAverageCPUUtilization`, `ECSServiceAverageMemoryUtilization`, `ALBRequestCountPerTarget` — ALB has no CPU metric
- ECS Anywhere (bare-metal/on-premises, SSM Agent + ECS Agent)
- EKS autoscaling: HPA/VPA (pod-level), Karpenter/Cluster Autoscaler (node-level)
- EKS aws-auth ConfigMap (IAM → RBAC mapping); Fargate pod execution role must differ from EC2 node group role
- EKS IRSA (pod-level IAM via OIDC + service account annotation)
- AWS SWF vs Step Functions (1-year duration, polling model, Decider/Activity Worker)
- AWS AppSync pipeline resolvers (chain multiple data sources in one GraphQL call)
- Elastic Beanstalk file/log storage (app files → S3, server logs → S3 or CloudWatch Logs)

### Networking
- Route 53 routing policies: Weighted (proportional split), Failover (active-passive only)
- CloudFront Cache-Control max-age=0 causes every request to hit origin
- Transit Gateway ECMP (scale VPN throughput; VGW does NOT support ECMP)
- Single VPN tunnel = 1.25 Gbps max; 2 tunnels per connection max
- Zonal RI vs Regional RI (Regional = billing discount only, no capacity guarantee)
- On-Demand Capacity Reservations (no term commitment, billing starts immediately)
- Wavelength Zones (inside 5G telco data centers, traffic never hits internet)
- Direct Connect Gateway (connects one DX to multiple VPCs across regions)
- NACL ephemeral port ranges by OS (Amazon Linux 32768–61000, Windows 49152–65535)
- DNS resolution + DNS hostnames required for public DNS hostname in non-default VPC

### Storage
- S3 static website: bucket name must match domain for Route 53 Alias
- S3 Glacier Expedited retrieval + Provisioned Capacity (3 retrievals/5 min, 150 MB/s)
- S3 Glacier Vault Lock (vault access policy vs vault lock policy, 24h test window, immutable after lock)
- S3 Object Lock (Compliance vs Governance mode, Legal Hold, retain-until-date)
- S3 Object Lambda (bucket name + object key identify the object; transform on GET)
- EFS lifecycle policy max 365 days (>365 days requires Lambda + DataSync workaround)
- EBS Elastic Volumes (live modification: type/size/IOPS/throughput; size only increases)
- Amazon DLM (snapshot/AMI lifecycle automation, cross-region copy)
- Storage Gateway Hardware Appliance (for non-virtualized / bare-metal environments)
- Storage service protocol reference table (all 9 services with protocols)

### Database
- RDS: `rds.force_ssl = true` (static param, requires reboot) for forcing SSL
- RDS: TDE (SQL Server + Oracle, at rest only — NOT in transit)
- RDS: IAM DB Auth = SSL automatically + EC2 instance profile credentials
- RDS: 35-day max automated backup; AWS Backup for longer retention
- RDS: Cannot auto-export automated snapshots to S3 (must copy → manual first)
- RDS Zonal vs Regional Reserved Instances (Regional = no capacity guarantee)
- Aurora: custom endpoints for subset-based routing (high/low capacity instances)
- Aurora: failover CNAME flip (<30s), single instance best-effort same AZ, Serverless different AZ
- Aurora: Provisioned → Serverless cannot convert in-place (use DMS with CDC)
- Aurora: Parallel Query (pushes computation to storage layer, MySQL only)
- Aurora: Backtrack (rewind in-place up to 72h, MySQL only, not DR)
- Aurora replicas: asynchronous (milliseconds) vs MySQL RDS replicas: asynchronous (seconds); Aurora up to 15, MySQL up to 5
- DynamoDB: Auto Scaling not enabled by default via CLI (only via console)

### Monitoring & Management
- CloudWatch default EC2 metrics vs metrics requiring CloudWatch Agent (memory, disk, swap, page file)
- CloudFormation: CreationPolicy + cfn-signal, UpdatePolicy, UpdateReplacePolicy, DependsOn
- CloudFormation helper scripts: cfn-init, cfn-signal, cfn-hup
- AWS X-Ray: traces/segments/subsegments, sampling, daemon, annotations vs metadata
- CloudWatch ServiceLens (unifies CloudWatch + X-Ray traces in one view)
- SQS: Short Polling (ReceiveMessageWaitTimeSeconds=0, subset of servers) vs Long Polling (>0, all servers, eliminates false empty responses)

### Security & IAM
- STS API calls: AssumeRole, AssumeRoleWithSAML, AssumeRoleWithWebIdentity, GetSessionToken, GetFederationToken
- ACM Private CA (private certs, org-only trust, per-CA cost)
- IAM Certificate Store (legacy, no auto-renewal, fallback when ACM unavailable)
- Third-party certs must be imported to ACM before associating to ALB

### Analytics
- Kinesis Firehose Lambda 5-minute timeout (synchronous, lightweight transforms only)
- Athena Federated Query is read-only (no write/insert/update to source)
- AppFlow (SaaS → AWS integration, no code, bi-directional, filter + transform)
- Redshift cross-region snapshot copy (per-cluster, not default, for DR)

---

## Global Infrastructure Service Scope

Covered in `02-AWS-Global-Infrastructure/README.md`:
- **Global**: IAM, Route 53, CloudFront, WAF (CloudFront), Global Accelerator, Organizations, Billing, DX Gateway, S3 bucket names
- **Regional**: VPC, S3 data, Lambda, API Gateway, ALB/NLB, ECS/EKS/ECR, DynamoDB, Aurora, RDS, ElastiCache, Kinesis, SNS/SQS/EventBridge, CloudWatch, CloudTrail, CloudFormation, EFS, ACM, KMS, Secrets Manager, Transit Gateway, and more
- **AZ-Specific**: EC2 instance, EBS volume, Subnet, NAT Gateway, RDS instance, ElastiCache node, EFS mount target
