## AWS CloudFormation

AWS CloudFormation is an **Infrastructure as Code (IaC)** service that lets you define and provision AWS resources using JSON or YAML templates.

**Key Concepts:**
- **Template**: A JSON/YAML file describing the AWS resources to create (EC2, S3, RDS, VPC, etc.).
- **Stack**: A collection of AWS resources created and managed as a single unit from a template.
- **Change Set**: Preview the changes CloudFormation will make before applying an update to a stack.
- **Drift Detection**: Detects when a resource's actual configuration has diverged from the template definition.
- **StackSets**: Deploy stacks across multiple AWS accounts and regions in a single operation.
- **Nested Stacks**: Reuse common template components by referencing other stacks within a template.

**How it works:** Write template → CloudFormation provisions resources in the correct order → manages dependencies automatically.

**Use cases:** Repeatable infrastructure deployments, disaster recovery, multi-environment setups (dev/staging/prod).
