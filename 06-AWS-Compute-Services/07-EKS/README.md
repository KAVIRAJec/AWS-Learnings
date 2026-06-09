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
- **IAM** handles **authentication** — verifies who you are.
- **Kubernetes RBAC** handles **authorization** — controls what you can do inside the cluster.
- IAM users/roles are NOT automatically granted Kubernetes permissions — they must be explicitly mapped via the `aws-auth` ConfigMap.

### aws-auth ConfigMap — IAM to RBAC Mapping

The `aws-auth` ConfigMap is how EKS grants IAM users and roles access to the Kubernetes cluster.

- Lives in the `kube-system` namespace on every EKS cluster.
- **Auto-created** when a managed node group is created — initially allows worker nodes to join the cluster.
- You add IAM user/role entries to this ConfigMap to grant Kubernetes RBAC permissions to those identities.
- The **AWS IAM Authenticator** runs on the EKS control plane — it reads `aws-auth` to resolve which Kubernetes RBAC role an IAM caller maps to.

```yaml
# aws-auth ConfigMap example
mapRoles:
  - rolearn: arn:aws:iam::123456789:role/MyDevRole
    username: dev-user
    groups:
      - system:masters        # full cluster admin
mapUsers:
  - userarn: arn:aws:iam::123456789:user/alice
    username: alice
    groups:
      - developers            # custom RBAC group
```

**Fargate pod execution role:**
- EKS Fargate requires a separate **pod execution IAM role** (`AmazonEKSFargatePodExecutionRole`) — used by the Fargate infrastructure to pull images, write logs, etc.
- This role must be **different** from the EC2 node group IAM role — Fargate profiles and EC2 node groups should not share the same IAM role.

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

---

## EKS Autoscaling

EKS supports two levels of autoscaling — **pod-level** (scale pods within existing nodes) and **node-level** (scale the number of EC2 nodes in the cluster).

### Pod-Level Autoscaling

Requires the **Kubernetes Metrics Server** to be installed — collects CPU/memory metrics from nodes and pods, which HPA and VPA use to make scaling decisions.

**Horizontal Pod Autoscaler (HPA) — scale out/in:**
- Automatically increases or decreases the **number of pod replicas** based on observed CPU utilization (or custom metrics).
- "Scale out and in" — adds/removes pods horizontally.
- Use case: traffic surges that need more instances of the same pod to handle load.

**Vertical Pod Autoscaler (VPA) — scale up/down:**
- Automatically adjusts the **CPU and memory requests/limits** of existing pods — resizes the pods rather than adding more.
- "Scale up and down" — changes resource allocation per pod.
- Use case: right-sizing pods that are under- or over-provisioned.
- **Not suitable** when the requirement is to scale in and out — VPA does not add new pods.

### Node-Level Autoscaling

**Cluster Autoscaler:**
- Native Kubernetes tool that adjusts the number of nodes when pods fail to schedule (not enough capacity) or nodes are underutilized.
- Uses **Auto Scaling Groups** under the hood.
- Slower to respond to changes and requires manual configuration and tuning — higher operational overhead.

**Karpenter (recommended):**
- AWS-built, high-performance cluster autoscaler — directly provisions EC2 instances without needing ASG configuration.
- Automatically selects the **right instance type and size** for pending pods — optimizes cost and performance.
- Faster to respond than Cluster Autoscaler — provisions nodes in seconds.
- Less operational overhead — no need to pre-configure ASGs or manage node group instance types.

**Autoscaling options comparison:**

| | HPA | VPA | Cluster Autoscaler | Karpenter |
|---|---|---|---|---|
| **What it scales** | Number of pods | Pod resource requests | Number of nodes (via ASG) | Number of nodes (direct EC2) |
| **Direction** | Horizontal (out/in) | Vertical (up/down) | Horizontal (nodes) | Horizontal (nodes) |
| **Requires Metrics Server** | Yes | Yes | No | No |
| **Response speed** | Fast | Moderate (pod restart) | Slower | Fast |
| **Operational overhead** | Low | Low | Medium | **Lowest** |
| **Best for** | Traffic spikes needing more pods | Right-sizing pod resources | Basic node scaling | Dynamic workloads needing fast, optimized node provisioning |

---

**Storage:**
- **EBS**: Block storage per pod (single AZ). Can be added via EBS CSI driver(Add-on).
- **EFS**: Shared persistent storage across pods and AZs (via CSI driver). Can be added via EFS CSI driver(Add-on).

**EKS vs ECS:**
- EKS: Kubernetes-native, more flexibility, steeper learning curve, portable across clouds.
- ECS: AWS-native, simpler to operate, tighter AWS integration, less portable.
