## Launch Template vs Launch Configuration

Both define the **instance configuration blueprint** used by Auto Scaling Groups (and direct instance launches) — AMI, instance type, key pair, security groups, user data, and storage settings.

### Launch Configuration *(legacy)*
- Older, simpler version — **it is not possible to modify a Launch Configuration once it is created**. To change anything (AMI, instance type, SG, user data), you must create a brand new Launch Configuration and update the ASG to point to it.
- Does **not** support multiple instance types, Spot + On-Demand mix, or placement groups.
- AWS has stopped adding new features to Launch Configurations — new workloads should use Launch Templates.

### Launch Template *(recommended)*
- **Versioned** — create multiple versions of the same template; specify which version the ASG uses (`$Latest`, `$Default`, or a pinned number).
- **Individual versions are immutable** — once a version is created, you cannot edit it. To change any setting (AMI, instance type, user data), you must create a **new version** of the template.
- Supports **mixed instance types** (e.g., m5.large + m5.xlarge) and **Spot + On-Demand mix** in a single ASG — critical for cost optimization.
- Supports placement groups, Capacity Reservations, Dedicated Hosts, and T2/T3 unlimited burst mode.
- Can launch individual instances directly (`aws ec2 run-instances --launch-template`) — not just ASGs.

**Updating an ASG to use a new AMI:**
1. Create a **new version** of the existing launch template (or a brand new launch template) with the updated AMI ID.
2. Update the ASG to point to the new template version.
3. Existing running instances are not replaced automatically — they continue using the old AMI until replaced (via instance refresh or natural scale-in/scale-out).

> You cannot start using a new AMI by modifying the existing template version — versions are immutable. A new version (or new template) is always required.

| | Launch Configuration | Launch Template |
|---|---|---|
| **Mutable** | No — recreate for any change | Yes — add a new version |
| **Versioning** | No | Yes |
| **Mixed instances / Spot + OD mix** | No | Yes |
| **Direct instance launch** | No | Yes |
| **AWS status** | Legacy (no new features) | Recommended |

---

### Instance Tenancy — Launch Template vs VPC

**Three tenancy options:**
- **Shared** (default) — instance shares physical hardware with other AWS accounts.
- **Dedicated Instance** — instance runs on single-tenant hardware, no other AWS customer on the same server.
- **Dedicated Host** — instance runs on a physical server fully dedicated to you. Required for BYOL (Bring Your Own License) and compliance use cases.

**How Launch Template tenancy interacts with VPC tenancy — dedicated always wins:**

| Launch Template Tenancy | VPC Tenancy | Result |
|---|---|---|
| Shared (default) | Default | Shared |
| Shared (default) | Dedicated | **Dedicated** ← VPC overrides |
| Dedicated | Default | **Dedicated** ← LT overrides |
| Dedicated | Dedicated | Dedicated |

> If either the Launch Template or the VPC is set to dedicated, the instance runs as dedicated — **dedicated always wins**.
