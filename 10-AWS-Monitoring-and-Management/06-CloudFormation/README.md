## AWS CloudFormation

**AWS CloudFormation** is an **Infrastructure as Code (IaC)** service — you describe your AWS infrastructure in a JSON or YAML template and CloudFormation provisions, updates, and deletes resources automatically in the correct dependency order.

- Free service — pay only for the AWS resources it creates (EC2, RDS, etc.).
- Declarative — you define *what* you want, CloudFormation figures out *how* and *in what order*.
- Every resource created by CloudFormation is tracked — it knows what it owns and can tear it all down cleanly.

---

## Template Structure

A CloudFormation template is a YAML/JSON file with these sections:

```yaml
AWSTemplateFormatVersion: "2010-09-09"   # optional, always this value
Description: "My infrastructure"          # optional

Parameters:                               # inputs — values passed at deploy time
  InstanceType:
    Type: String
    Default: t3.micro
    AllowedValues: [t3.micro, t3.small]

Mappings:                                 # static lookup tables (region → AMI, env → size)
  RegionAMI:
    us-east-1:
      AMI: ami-0abcdef1234567890

Conditions:                               # conditional resource creation
  IsProd: !Equals [!Ref Env, "production"]

Resources:                                # REQUIRED — the actual AWS resources
  MyEC2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !FindInMap [RegionAMI, !Ref AWS::Region, AMI]

Outputs:                                  # values exported after stack creation
  InstanceId:
    Value: !Ref MyEC2
    Export:
      Name: MyEC2InstanceId
```

Only **Resources** is mandatory — all other sections are optional.

---

## Key Concepts

**Stack:**
- A collection of AWS resources created and managed together from one template.
- Create/update/delete the stack → CloudFormation creates/updates/deletes all its resources together.
- Resources in a stack cannot be manually deleted without deleting the stack (unless you use retain policies).

**Change Set:**
- Preview exactly what will change before applying an update — which resources will be added, modified, or replaced.
- Replaced means the old resource is deleted and a new one is created (e.g. changing an RDS instance type = replacement = data loss risk).

```
Current stack ──► submit updated template ──► Change Set (preview diff) ──► execute ──► updated stack
```

**Drift Detection:**
- Detects when a resource's actual configuration has diverged from what the template defines — e.g. someone manually changed a security group rule via console.
- Does not auto-fix drift — it only reports it. You decide whether to update the template or fix the resource.

**Stack Policy:**
- A JSON policy attached to a stack that controls which resources can be updated or replaced during a stack update.
- Prevents accidental replacement of critical resources (e.g. RDS, DynamoDB).

---

## StackSets (Multi-Account / Multi-Region)

**StackSets** deploy the same CloudFormation stack across multiple AWS accounts and regions in one operation — managed from a single administrator account.

```
Administrator Account
    │
    ├──► Account A, us-east-1  (stack instance)
    ├──► Account A, eu-west-1  (stack instance)
    ├──► Account B, us-east-1  (stack instance)
    └──► Account C, ap-south-1 (stack instance)
```

- Used to enforce baseline infrastructure (VPC, IAM roles, CloudTrail, Config rules) across an AWS Organization.
- Integrates with AWS Organizations — auto-deploy to new accounts as they join.

---

## Nested Stacks

Break large templates into smaller reusable components — a parent stack references child stacks via `AWS::CloudFormation::Stack`.

```
Parent Stack (main.yaml)
    │
    ├── Nested Stack: network.yaml   (VPC, subnets, IGW)
    ├── Nested Stack: security.yaml  (security groups, NACLs)
    └── Nested Stack: app.yaml       (EC2, ALB, Auto Scaling)
```

- Child stacks are independent stacks — they can be reused across multiple parent stacks.
- Changes to a child template propagate when the parent stack is updated.
- Best practice: separate concerns — networking, security, compute in different nested stacks.

---

## Intrinsic Functions

Built-in functions used inside templates to reference values dynamically:

| Function | Purpose |
|---|---|
| `!Ref` | Reference a parameter or resource (returns resource ID) |
| `!GetAtt` | Get an attribute of a resource (e.g. `!GetAtt MyEC2.PublicIp`) |
| `!Sub` | String substitution — `!Sub "arn:aws:s3:::${BucketName}"` |
| `!FindInMap` | Look up a value in a Mappings table |
| `!If` | Conditional value — `!If [IsProd, large, small]` |
| `!ImportValue` | Import an Output exported from another stack |
| `!Join` | Join strings — `!Join [":", [a, b, c]]` → `a:b:c` |

---

## Rollback Behaviour

- On **stack creation failure**: CloudFormation rolls back and deletes all resources created so far (default).
- On **stack update failure**: CloudFormation rolls back to the previous working state.
- You can disable rollback for debugging — leaves resources in failed state so you can inspect what went wrong.

---

## Application Composer

**AWS Application Composer** is a visual drag-and-drop designer built into the AWS console that lets you design serverless architectures visually — it generates the **CloudFormation / SAM template** for you as you build.

- Drag AWS resources (Lambda, API Gateway, DynamoDB, SQS, S3, etc.) onto a canvas and connect them.
- Template (YAML) is generated and updated live as you design — you can also edit the YAML directly and see the canvas update.
- Works with **AWS SAM (Serverless Application Model)** templates — a CloudFormation extension with simplified syntax for serverless resources.
- Integrated with **VS Code** via the AWS Toolkit extension — design locally without opening the console.

> Application Composer is a design/authoring tool — it does not deploy resources itself. It produces a template that you then deploy via CloudFormation or SAM CLI.

---

## CloudFormation vs Terraform vs CDK

| | CloudFormation | Terraform | AWS CDK |
|---|---|---|---|
| **Language** | YAML / JSON | HCL (HashiCorp) | Python, TypeScript, Java, Go |
| **Provider** | AWS only | Multi-cloud | AWS only (compiles to CloudFormation) |
| **State management** | Managed by AWS (stack) | State file (local or remote) | Managed by AWS (via CloudFormation) |
| **Drift detection** | Yes | Yes (`terraform plan`) | Yes (via CloudFormation) |
| **Learning curve** | Medium | Medium | Low (use real programming language) |
| **Best for** | AWS-native IaC, StackSets | Multi-cloud, existing Terraform teams | Developers who prefer code over YAML |
