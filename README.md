# AWS Learning Vault ☁️

> A structured, in-depth personal knowledge base covering every major AWS service and concept —
> built for the **AWS Certified Solutions Architect – Associate (SAA-C03)** and beyond.

---

```
 ██████╗ ██╗      ██████╗ ██╗   ██╗██████╗
██╔════╝ ██║     ██╔═══██╗██║   ██║██╔══██╗
██║      ██║     ██║   ██║██║   ██║██║  ██║
██║      ██║     ██║   ██║██║   ██║██║  ██║
╚██████╗ ███████╗╚██████╔╝╚██████╔╝██████╔╝
 ╚═════╝ ╚══════╝ ╚═════╝  ╚═════╝ ╚═════╝
        A W S   L E A R N I N G   V A U L T
```

---

## What's Inside

This repository is my **personal AWS reference** — every concept written from scratch, every service explained the way I understand it. Not copy-pasted docs. Not surface-level bullet points. Real depth.

- Every major AWS service documented with diagrams, flow charts, and comparison tables.
- Concepts built in layers — fundamentals first, then advanced patterns.
- Written in a format that makes re-reading fast and re-learning unnecessary.

---

## Certifications Covered

| Certification | Level |
|---|---|
| AWS Certified Cloud Practitioner (CLF-C02) | Foundational |
| AWS Certified Solutions Architect – Associate (SAA-C03) | Associate |

---

## Index

### 01 · Cloud Concepts
> The *why* behind cloud — benefits, models, and core principles.

- [Cloud Concepts](./01-Cloud-Concepts/README.md)

---

### 02 · AWS Global Infrastructure
> Regions, Availability Zones, Edge Locations, Local Zones — how AWS is physically built.

- [Global Infrastructure](./02-AWS-Global-Infrastructure/README.md)

---

### 03 · Cloud Architecture
> Frameworks and strategies for designing well-built systems on AWS.

| Topic | Link |
|---|---|
| Well-Architected Framework (6 Pillars) | [→](./03-Cloud-Architecture/01-Well-Architected-Framework/README.md) |
| Cloud Adoption Framework (CAF) | [→](./03-Cloud-Architecture/02-Cloud-Adoption-Framework/README.md) |
| Disaster Recovery Strategies | [→](./03-Cloud-Architecture/03-Disaster-Recovery/README.md) |
| Support Plans | [→](./03-Cloud-Architecture/04-Support-Plans/README.md) |
| HIPAA on AWS | [→](./03-Cloud-Architecture/05-HIPAA/README.md) |
| AWS Connect | [→](./03-Cloud-Architecture/06-AWS-Connect/README.md) |

---

### 04 · Identity & Access Management (IAM)
> Who can do what, on which resource, and under what conditions.

| Topic | Link |
|---|---|
| IAM Users & Groups | [→](./04-AWS-Identity-and-Access-Management/01-IAM-Users-Groups/README.md) |
| IAM Policies | [→](./04-AWS-Identity-and-Access-Management/02-IAM-Policies/README.md) |
| IAM Roles | [→](./04-AWS-Identity-and-Access-Management/03-IAM-Roles/README.md) |
| Permission Evaluation Logic | [→](./04-AWS-Identity-and-Access-Management/04-IAM-Permission-Evaluation/README.md) |
| Root Account | [→](./04-AWS-Identity-and-Access-Management/05-Root-Account/README.md) |
| IAM Identity Center (SSO) | [→](./04-AWS-Identity-and-Access-Management/06-IAM-Identity-Center/README.md) |
| AWS Organizations | [→](./04-AWS-Identity-and-Access-Management/07-AWS-Organizations/README.md) |
| AWS Control Tower | [→](./04-AWS-Identity-and-Access-Management/08-AWS-Control-Tower/README.md) |

---

### 05 · Networking Services
> VPC, connectivity, and traffic management — the backbone of every AWS architecture.

**VPC Deep Dive**

| Topic | Link |
|---|---|
| Subnets & CIDR | [→](./05-AWS-Networking-Services/01-VPC/01-Subnets/README.md) |
| Internet Gateway | [→](./05-AWS-Networking-Services/01-VPC/02-Internet-Gateway/README.md) |
| Route Tables | [→](./05-AWS-Networking-Services/01-VPC/03-Route-Tables/README.md) |
| NAT Gateway | [→](./05-AWS-Networking-Services/01-VPC/04-NAT-Gateway/README.md) |
| Security Groups | [→](./05-AWS-Networking-Services/01-VPC/05-Security-Groups/README.md) |
| Network ACLs | [→](./05-AWS-Networking-Services/01-VPC/06-Network-ACLs/README.md) |
| VPC Peering | [→](./05-AWS-Networking-Services/01-VPC/07-VPC-Peering/README.md) |
| VPC Endpoints | [→](./05-AWS-Networking-Services/01-VPC/08-VPC-Endpoints/README.md) |
| VPC Flow Logs | [→](./05-AWS-Networking-Services/01-VPC/09-VPC-Flow-Logs/README.md) |
| VPC Traffic Mirroring | [→](./05-AWS-Networking-Services/01-VPC/10-VPC-Traffic-Mirroring/README.md) |
| Bastion Host | [→](./05-AWS-Networking-Services/01-VPC/11-Bastion-Host/README.md) |
| IPv6 & Egress-Only IGW | [→](./05-AWS-Networking-Services/01-VPC/12-IPv6/README.md) |

**Connectivity & Delivery**

| Service | Link |
|---|---|
| Route 53 | [→](./05-AWS-Networking-Services/02-Route-53/README.md) |
| Direct Connect | [→](./05-AWS-Networking-Services/03-Direct-Connect/README.md) |
| CloudFront | [→](./05-AWS-Networking-Services/04-CloudFront/README.md) |
| Global Accelerator | [→](./05-AWS-Networking-Services/05-Global-Accelerator/README.md) |
| Transit Gateway | [→](./05-AWS-Networking-Services/06-Transit-Gateway/README.md) |
| PrivateLink | [→](./05-AWS-Networking-Services/07-PrivateLink/README.md) |
| AWS VPN (Site-to-Site, Client, CloudHub) | [→](./05-AWS-Networking-Services/08-AWS-VPN/README.md) |

---

### 06 · Compute Services
> From bare-metal EC2 to serverless Lambda — every way to run code on AWS.

| Service | Link |
|---|---|
| EC2 (instances, AMI, pricing, ENI, ENA, SSM) | [→](./06-AWS-Compute-Services/01-EC2/README.md) |
| Lambda | [→](./06-AWS-Compute-Services/02-Lambda/README.md) |
| ECS (Elastic Container Service) | [→](./06-AWS-Compute-Services/03.ECS/README.md) |
| ECR (Elastic Container Registry) | [→](./06-AWS-Compute-Services/04.ECR/README.md) |
| Lightsail | [→](./06-AWS-Compute-Services/05-Lightsail/README.md) |
| Batch | [→](./06-AWS-Compute-Services/06-Batch/README.md) |
| EKS (Elastic Kubernetes Service) | [→](./06-AWS-Compute-Services/07-EKS/README.md) |
| Fargate | [→](./06-AWS-Compute-Services/08-Fargate/README.md) |
| Elastic Beanstalk | [→](./06-AWS-Compute-Services/09-Elastic-Beanstalk/README.md) |
| App Runner | [→](./06-AWS-Compute-Services/10-App-Runner/README.md) |
| App2Container | [→](./06-AWS-Compute-Services/11-App2Container/README.md) |
| API Gateway | [→](./06-AWS-Compute-Services/12-API-Gateway/README.md) |
| Step Functions | [→](./06-AWS-Compute-Services/13-Step-Functions/README.md) |

---

### 07 · Storage Services
> Object, block, file, and hybrid storage — from S3 to Snow Family.

| Service | Link |
|---|---|
| S3 (Simple Storage Service) | [→](./07-AWS-Storage-Services/01-S3/README.md) |
| EBS (Elastic Block Store) | [→](./07-AWS-Storage-Services/02-EBS/README.md) |
| EFS (Elastic File System) | [→](./07-AWS-Storage-Services/03-EFS/README.md) |
| FSx | [→](./07-AWS-Storage-Services/04-FSx/README.md) |
| AWS Backup | [→](./07-AWS-Storage-Services/05-AWS-Backup/README.md) |
| Storage Gateway | [→](./07-AWS-Storage-Services/06-Storage-Gateway/README.md) |
| DataSync | [→](./07-AWS-Storage-Services/07-DataSync/README.md) |
| Snow Family | [→](./07-AWS-Storage-Services/08-AWS-Snow-Family/README.md) |
| Transfer Family | [→](./07-AWS-Storage-Services/09-AWS-Transfer-Family/README.md) |

---

### 08 · Database Services
> Relational, NoSQL, in-memory, graph, ledger — the full spectrum of AWS databases.

| Service | Link |
|---|---|
| RDS | [→](./08-AWS-Database-Services/01-RDS/README.md) |
| DynamoDB | [→](./08-AWS-Database-Services/02-DynamoDB/README.md) |
| Aurora (+ Migration paths) | [→](./08-AWS-Database-Services/03-Aurora/README.md) |
| Redshift | [→](./08-AWS-Database-Services/04-Redshift/README.md) |
| ElastiCache | [→](./08-AWS-Database-Services/05-ElastiCache/README.md) |
| Neptune | [→](./08-AWS-Database-Services/06-Neptune/README.md) |
| DocumentDB | [→](./08-AWS-Database-Services/07-DocumentDB/README.md) |
| Timestream | [→](./08-AWS-Database-Services/08-Timestream/README.md) |
| Keyspaces | [→](./08-AWS-Database-Services/09-Keyspaces/README.md) |
| QLDB | [→](./08-AWS-Database-Services/10-QLDB/README.md) |
| DMS + RDS & Aurora Migrations | [→](./08-AWS-Database-Services/11-DMS/README.md) |

---

### 09 · Cost Optimization
> Understand, control, and reduce your AWS spend.

- [Cost Optimization](./09-AWS-Cost-Optimization/README.md)

---

### 10 · Monitoring, Management & Developer Services
> Observe, secure, automate, and operate your AWS environment.

| Service | Link |
|---|---|
| CloudWatch | [→](./10-AWS-Monitoring-and-Management/01-CloudWatch/README.md) |
| CloudTrail | [→](./10-AWS-Monitoring-and-Management/02-CloudTrail/README.md) |
| AWS Config | [→](./10-AWS-Monitoring-and-Management/03-Config/README.md) |
| Systems Manager (SSM) | [→](./10-AWS-Monitoring-and-Management/04-Systems-Manager/README.md) |
| Trusted Advisor | [→](./10-AWS-Monitoring-and-Management/05-Trusted-Advisor/README.md) |
| CloudFormation + Application Composer | [→](./10-AWS-Monitoring-and-Management/06-CloudFormation/README.md) |
| Health Dashboard | [→](./10-AWS-Monitoring-and-Management/07-Health-Dashboard/README.md) |
| SNS | [→](./10-AWS-Monitoring-and-Management/08-SNS/README.md) |
| SQS | [→](./10-AWS-Monitoring-and-Management/09-SQS/README.md) |
| Amazon MQ | [→](./10-AWS-Monitoring-and-Management/10-Amazon-MQ/README.md) |
| OpsWorks | [→](./10-AWS-Monitoring-and-Management/11-OpsWorks/README.md) |
| Outposts | [→](./10-AWS-Monitoring-and-Management/12-Outposts/README.md) |
| Cognito | [→](./10-AWS-Monitoring-and-Management/13-Cognito/README.md) |
| KMS + Encryption (SSE, CSE, Envelope) | [→](./10-AWS-Monitoring-and-Management/14-KMS/README.md) |
| Secrets Manager | [→](./10-AWS-Monitoring-and-Management/15-Secrets-Manager/README.md) |
| Parameter Store | [→](./10-AWS-Monitoring-and-Management/16-Parameter-Store/README.md) |
| CloudHSM | [→](./10-AWS-Monitoring-and-Management/17-CloudHSM/README.md) |
| EventBridge | [→](./10-AWS-Monitoring-and-Management/18-EventBridge/README.md) |
| SES | [→](./10-AWS-Monitoring-and-Management/19-SES/README.md) |
| Pinpoint | [→](./10-AWS-Monitoring-and-Management/20-Pinpoint/README.md) |

---

### 11 · CI/CD
> Build, test, deploy — automating the software delivery lifecycle on AWS.

- [CodeCommit · CodeBuild · CodePipeline · CodeDeploy · Amplify](./11-AWS-CICD/README.md)

---

### 12 · Infrastructure as Code (IaC)
> Define infrastructure in code — repeatable, version-controlled, automated deployments.

- [IaC Tools (CDK, SAM, Terraform)](./12-AWS-IaC-Tools/README.md)

---

### 13 · Data & Analytics Services
> Ingest, process, query, and visualize data at any scale.

| Service | Link |
|---|---|
| Athena | [→](./13-Data-Analytics-Services/01-Athena/README.md) |
| Glue | [→](./13-Data-Analytics-Services/02-Glue/README.md) |
| Redshift | [→](./13-Data-Analytics-Services/03-Redshift/README.md) |
| Kinesis | [→](./13-Data-Analytics-Services/04-Kinesis/README.md) |
| QuickSight | [→](./13-Data-Analytics-Services/05-QuickSight/README.md) |
| EMR | [→](./13-Data-Analytics-Services/06-EMR/README.md) |
| OpenSearch | [→](./13-Data-Analytics-Services/07-OpenSearch/README.md) |
| Lake Formation | [→](./13-Data-Analytics-Services/08-Lake-Formation/README.md) |
| MSK (Managed Kafka) | [→](./13-Data-Analytics-Services/09-MSK/README.md) |
| Data Pipeline | [→](./13-Data-Analytics-Services/10-Data-Pipeline/README.md) |
| AppFlow | [→](./13-Data-Analytics-Services/11-AppFlow/README.md) |

---

### 14 · Machine Learning Services
> AI/ML — from pre-built models to custom training pipelines.

| Service | Link |
|---|---|
| SageMaker | [→](./14-Machine-Learning-Services/01-SageMaker/README.md) |
| Rekognition | [→](./14-Machine-Learning-Services/02-Rekognition/README.md) |
| Comprehend | [→](./14-Machine-Learning-Services/03-Comprehend/README.md) |
| Lex + Connect | [→](./14-Machine-Learning-Services/04-Lex-Connect/README.md) |
| Polly | [→](./14-Machine-Learning-Services/05-Polly/README.md) |
| Transcribe | [→](./14-Machine-Learning-Services/06-Transcribe/README.md) |
| Translate | [→](./14-Machine-Learning-Services/07-Translate/README.md) |
| Personalize | [→](./14-Machine-Learning-Services/08-Personalize/README.md) |
| Forecast | [→](./14-Machine-Learning-Services/09-Forecast/README.md) |
| Textract | [→](./14-Machine-Learning-Services/10-Textract/README.md) |
| Fraud Detector | [→](./14-Machine-Learning-Services/11-Fraud-Detector/README.md) |
| Kendra | [→](./14-Machine-Learning-Services/12-Kendra/README.md) |
| CodeGuru | [→](./14-Machine-Learning-Services/13-CodeGuru/README.md) |
| Deep Learning AMIs | [→](./14-Machine-Learning-Services/14-Deep-Learning-AMIs/README.md) |
| DeepRacer | [→](./14-Machine-Learning-Services/15-DeepRacer/README.md) |

---

### 15 · Security Services
> Protect, detect, and respond — AWS's dedicated security toolset.

| Service | Link |
|---|---|
| ACM (Certificate Manager) | [→](./15-AWS-Security-Services/01-ACM/README.md) |
| WAF (Web Application Firewall) | [→](./15-AWS-Security-Services/02-WAF/README.md) |
| Shield (DDoS Protection) | [→](./15-AWS-Security-Services/03-Shield/README.md) |
| Firewall Manager | [→](./15-AWS-Security-Services/04-Firewall-Manager/README.md) |
| GuardDuty | [→](./15-AWS-Security-Services/05-GuardDuty/README.md) |
| Inspector | [→](./15-AWS-Security-Services/06-Inspector/README.md) |
| Security Hub | [→](./15-AWS-Security-Services/07-Security-Hub/README.md) |
| Macie | [→](./15-AWS-Security-Services/08-Macie/README.md) |
| Network Firewall | [→](./15-AWS-Security-Services/09-Network-Firewall/README.md) |

---

### 16 · Migration Services
> Discover, plan, and execute migrations to AWS.

| Service | Link |
|---|---|
| Application Discovery Service | [→](./16-AWS-Migration-Services/01-Application-Discovery-Service/README.md) |
| Application Migration Service (MGN) | [→](./16-AWS-Migration-Services/02-Application-Migration-Service/README.md) |

---

## Stats

```
16  domains covered
100+ services documented
       ┌─────────────────────────────────────────────┐
       │  Networking  ████████████████  12 topics    │
       │  Compute     █████████████     13 services  │
       │  Database    ████████████      11 services  │
       │  Security    ██████████         9 services  │
       │  Monitoring  █████████████████ 20 services  │
       │  ML / AI     █████████████     15 services  │
       └─────────────────────────────────────────────┘
```

---

## How to Navigate

```
Each section  →  section README  (overview + table of contents)
      │
      └──► individual topic README  (deep dive with diagrams + tables)
```

> Built with focus. Written with depth. Designed to last.

---

*AWS Certified Solutions Architect — Associate (SAA-C03)*
*by [@KAVIRAJec](https://github.com/KAVIRAJec)*
