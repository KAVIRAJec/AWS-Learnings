## Amazon EKS (Elastic Kubernetes Service)

Amazon EKS is a fully managed **Kubernetes** service — runs Kubernetes on AWS without needing to install or operate the control plane. Supports standard Kubernetes tools (kubectl, Helm, etc.).

![alt text](image.png)

**Key Concepts:**
- **Control Plane**: Fully managed by AWS — includes API server, etcd, scheduler, and controller manager. Runs across multiple AZs automatically.
- **Worker Nodes**: Run your actual workloads (pods). You manage these via node groups or Fargate.
- **Pod**: Smallest deployable unit in Kubernetes — one or more containers sharing network and storage.
- **Node Group**: A collection of EC2 instances that serve as Kubernetes worker nodes in a cluster. All instances in a node group share the same instance type, AMI, and configuration. EKS registers them automatically with the control plane so pods can be scheduled on them. These are managed by AWS Auto Scaling Groups for scaling and lifecycle management.

**Node Types:**

| Type | Description |
|------|-------------|
| **Self-Managed Nodes** | You provision and manage EC2 instances manually (Supports on-demand and spot instances) |
| **Managed Node Groups** | AWS manages EC2 lifecycle (launch, update, terminate) — you define instance type and scaling (Supports on-demand and spot instances) |
| **Fargate** | Serverless — AWS provisions compute per pod, no nodes to manage |

**Networking:**
- Uses **AWS VPC CNI plugin** — each pod gets a real VPC IP address from the subnet.
- Supports security groups at the pod level.

**IAM & Security:**
- Supports Kubernetes RBAC alongside AWS IAM for access control.

### IRSA — IAM Roles for Service Accounts

IRSA is the AWS-recommended method for giving fine-grained IAM permissions to individual Pods in an EKS cluster.

**Problem without IRSA** — all pods on a node inherit the node's IAM role. No pod-level isolation. One compromised pod exposes all permissions the node holds.

**What IRSA does** — binds a specific IAM Role to a specific Kubernetes Service Account. Only pods using that SA get those permissions.

```
Worker Node (node-role → broad permissions)
  ├── Pod A  uses SA: s3-reader  → assumes s3-read-only IAM role  ✓
  └── Pod B  uses SA: default    → no AWS permissions              ✓
```

**How it works:**
1. Each EKS cluster has an **OIDC issuer URL** — register it as an IAM Identity Provider so IAM trusts tokens from this cluster.
2. Create an **IAM Role** whose trust policy allows only a specific `namespace:serviceaccount` to assume it via `sts:AssumeRoleWithWebIdentity`.
3. **Annotate the Kubernetes Service Account** with the IAM role ARN (`eks.amazonaws.com/role-arn`).
4. When the pod starts, EKS automatically injects a **short-lived JWT token** and sets `AWS_ROLE_ARN` + `AWS_WEB_IDENTITY_TOKEN_FILE` env vars into the container.
5. The **AWS SDK** reads those env vars, calls STS with the JWT → gets back temporary credentials. Pod never holds long-lived credentials.

**Key properties:**
- Trust policy is locked to `namespace + service account` — same SA name in a different namespace cannot assume the role.
- Credentials are short-lived STS tokens (~1 hour), auto-refreshed by the SDK.
- Works on Fargate (no node-level role exists on Fargate anyway).
- Every `AssumeRoleWithWebIdentity` call is logged in CloudTrail with the SA identity.

| | Node IAM Role | IRSA |
|---|---|---|
| **Scope** | All pods on the node | Per Service Account |
| **Least privilege** | Hard to enforce | Fully enforceable |
| **Credentials** | Long-lived instance profile | Short-lived STS tokens |
| **Blast radius** | Entire node's permissions | Only that SA's permissions |
| **Fargate support** | No | Yes |

**Storage:**
- **EBS**: Block storage per pod (single AZ). Can be added via EBS CSI driver(Add-on).
- **EFS**: Shared persistent storage across pods and AZs (via CSI driver). Can be added via EFS CSI driver(Add-on).

**EKS vs ECS:**
- EKS: Kubernetes-native, more flexibility, steeper learning curve, portable across clouds.
- ECS: AWS-native, simpler to operate, tighter AWS integration, less portable.
