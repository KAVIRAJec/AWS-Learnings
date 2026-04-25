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
