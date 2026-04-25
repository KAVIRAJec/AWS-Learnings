## AWS OpsWorks

AWS OpsWorks is a **configuration management service** that automates server configuration, deployment, and management using **Chef** or **Puppet**.

- **Chef**: Uses "recipes" and "cookbooks" to define server configurations and automate tasks.
- **Puppet**: Uses "manifests" to automate deployment, configuration, and management of infrastructure.
- Supports EC2 instances and on-premises servers.
- Manages the full application lifecycle: setup, configure, deploy, undeploy, shutdown.

**Note**: OpsWorks is a legacy service. AWS recommends **Systems Manager** or **AWS CDK/CloudFormation** for new workloads.
