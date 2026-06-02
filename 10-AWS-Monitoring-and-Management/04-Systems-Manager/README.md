## AWS Systems Manager (SSM)

AWS Systems Manager is an operations management service to view, control, and automate AWS and on-premises infrastructure at scale — without needing SSH or RDP access.

**Key Features:**
- **Session Manager**: Secure shell access to EC2/on-premises instances via browser or CLI — no SSH keys, no open ports needed.
- **Patch Manager**: Automates OS and application patching across instances on a schedule.
- **Run Command**: Execute scripts or commands across multiple instances without logging in.
- **Parameter Store**: Secure storage for configuration data and secrets (see separate notes).
- **Inventory**: Collects metadata about instances (OS, installed software, network config).
- **State Manager**: Ensures instances maintain a defined configuration state (e.g., antivirus always running).
- **Automation**: Runs predefined or custom runbooks to automate common maintenance tasks (e.g., restart instance, create AMI).

**SSM Agent**: Must be installed on instances (pre-installed on most AWS AMIs). Instances need an IAM role with SSM permissions.

---

## Run Command

**Run Command** lets you execute shell scripts, PowerShell commands, or AWS-managed documents (SSM Documents) across **multiple instances simultaneously** — without SSH or RDP.

- Target instances by **instance ID**, **tags** (e.g. `Env=prod`), or **resource groups**.
- Commands are executed via the SSM Agent — no open ports needed.
- Output is sent to the **console**, **S3**, or **CloudWatch Logs**.
- Full **audit trail** in CloudTrail — who ran what command, on which instance, when.

```
You (console / CLI / EventBridge)
    │
    │  send command + target (tag: Env=prod)
    ▼
SSM Run Command
    │  dispatches to all matching instances via SSM Agent
    ├──► EC2-A  ──► runs script ──► output → S3 / CloudWatch
    ├──► EC2-B  ──► runs script ──► output → S3 / CloudWatch
    └──► EC2-C  ──► runs script ──► output → S3 / CloudWatch
```

**SSM Documents (commands):**
- **AWS-RunShellScript** — run any shell commands (Linux).
- **AWS-RunPowerShellScript** — run PowerShell (Windows).
- **AWS-RunAnsiblePlaybook** — run Ansible playbooks.
- Custom documents — define your own reusable command sequences.

**Use cases:** Install software, collect logs, restart services, run health checks across a fleet.

---

## Patch Manager

**Patch Manager** automates patching of OS and applications across EC2 and on-premises instances — defines *what* to patch and *when* to patch using patch baselines and maintenance windows.

### Patch Baseline
Defines which patches are **approved** or **rejected** for installation:
- **AWS-managed baselines**: pre-built per OS (e.g. `AWS-AmazonLinux2DefaultPatchBaseline`, `AWS-WindowsDefaultPatchBaseline`) — auto-approves security patches after 7 days.
- **Custom baselines**: define your own approval rules (severity, classification, specific CVEs to include/exclude).

### Patch Group
- A tag (`Patch Group = prod`) applied to instances — associates instances to a specific patch baseline.
- Separates patching policies across environments (prod uses stricter baseline than dev).

```
Instances tagged: PatchGroup=prod  ──► Custom Prod Baseline (critical patches only)
Instances tagged: PatchGroup=dev   ──► AWS Default Baseline (all patches)
```

### How Patching Works
```
1. Patch Manager scans instances → compares installed packages vs approved patches in baseline
2. Reports compliance: Compliant / Non-Compliant
3. On scheduled Maintenance Window → installs approved missing patches via SSM Agent
4. Compliance report → S3 / AWS Security Hub / Config
```

- Patch state stored in **SSM Inventory** — queryable per instance.
- Works for: Amazon Linux, RHEL, Ubuntu, Windows Server, SUSE.

---

## Maintenance Window

**Maintenance Window** defines a scheduled time slot during which SSM tasks (patching, Run Command, Automation) are allowed to run — prevents disruptive operations from running during peak hours.

- Define: **schedule** (cron/rate), **duration** (e.g. 2 hours), **cutoff** (stop initiating new tasks 30 min before window ends).
- Register **targets** (instances by tag or resource group) and **tasks** (Run Command, Patch Manager, Automation, Lambda, Step Functions).

```
Maintenance Window: Every Sunday 2:00 AM – 4:00 AM (duration: 2h, cutoff: 30min)
    │
    ├── Task 1: Run Command — backup script on tag:Env=prod
    └── Task 2: Patch Manager — install approved patches on tag:PatchGroup=prod

Timeline:
  02:00 ──► tasks start
  03:30 ──► cutoff (no new tasks initiated after this)
  04:00 ──► window ends (running tasks may complete)
```

- Multiple tasks can run concurrently within a window.
- **Error threshold** — stop the window if N% of targets fail.
- **Concurrency** — control how many instances are patched simultaneously (e.g. patch 10% at a time for rolling updates).

---

## Automation

**SSM Automation** runs **runbooks** (SSM Automation Documents) to automate operational tasks — more powerful than Run Command because it can orchestrate multi-step workflows across AWS services, not just run scripts on instances.

- Pre-built AWS runbooks: `AWS-RestartEC2Instance`, `AWS-CreateImage`, `AWS-StopEC2InstanceWithApproval`, `AWS-PatchInstanceWithRollback`.
- Custom runbooks: define steps in YAML/JSON — each step can invoke a Lambda, run a script, call an API, wait for approval.

### Common Use Cases

**Zero-downtime patching for load-balanced instances — `AWSEC2-PatchLoadBalancerInstance`:**

A pre-built AWS Automation document specifically designed for patching EC2 instances that are part of an ALB target group — without dropping traffic or causing downtime.

```
Maintenance Window triggers AWSEC2-PatchLoadBalancerInstance
        │
        ▼
Step 1: Deregister instance from ALB target group
        │
        ▼
Step 2: Wait for in-flight requests to drain (connection draining)
        │
        ▼
Step 3: Apply patches via Patch Manager (reboot if required)
        │
        ▼
Step 4: Re-register instance into ALB target group
        │
        ▼
Step 5: Wait for health check to pass → done
```

- No manual steps — the entire deregister → patch → re-register cycle is automated.
- Schedule via **Maintenance Window** to run during off-peak hours.
- Combine with **concurrency control** in the Maintenance Window (e.g., patch one instance at a time) to keep the rest of the fleet serving traffic throughout.
- All steps logged in CloudTrail for compliance audit.

```
Example: Patch with rollback runbook
  Step 1: Create AMI (snapshot before patching)
  Step 2: Apply patches via Patch Manager
  Step 3: Verify instance health (HTTP check)
  Step 4: If health check fails → restore from AMI (automatic rollback)
```

```
Example: Stop instances after business hours (triggered by EventBridge)
  Step 1: Get all instances with tag: Env=dev
  Step 2: Stop each instance
  Step 3: Send notification via SNS
```

### Execution Modes
- **Manual** — trigger from console or CLI.
- **Scheduled** — via Maintenance Window or EventBridge cron.
- **Event-driven** — EventBridge rule triggers automation on an AWS event (e.g. EC2 state change).
- **Human approval step** — pause automation and wait for a human to approve via SNS notification before continuing.

---

## SSM Features Summary

| Feature | What it does | Runs on |
|---|---|---|
| **Session Manager** | Interactive shell — no SSH/port 22 | Instances |
| **Run Command** | Execute scripts/commands across a fleet | Instances |
| **Patch Manager** | Automate OS/app patching with baselines | Instances |
| **Maintenance Window** | Schedule when tasks are allowed to run | Tasks |
| **Automation** | Multi-step operational runbooks across AWS services | AWS resources |
| **Parameter Store** | Secure config/secrets storage | Applications |
| **Inventory** | Collect metadata about instances and software | Instances |
| **State Manager** | Enforce desired configuration state | Instances |
