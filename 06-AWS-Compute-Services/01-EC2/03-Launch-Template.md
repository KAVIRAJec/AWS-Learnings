## Launch Template vs Launch Configuration

Both define the **instance configuration blueprint** used by Auto Scaling Groups (and direct instance launches) — AMI, instance type, key pair, security groups, user data, and storage settings.

### Launch Configuration *(legacy)*
- Older, simpler version — **it is not possible to modify a Launch Configuration once it is created**. To change anything (AMI, instance type, SG, user data), you must create a brand new Launch Configuration and update the ASG to point to it.
- Does **not** support multiple instance types, Spot + On-Demand mix, or placement groups.
- AWS has stopped adding new features to Launch Configurations — new workloads should use Launch Templates.

### Launch Template *(recommended)*
- **Versioned** — create multiple versions of the same template; specify which version the ASG uses (latest, default, or a pinned number).
- Supports **mixed instance types** (e.g., m5.large + m5.xlarge) and **Spot + On-Demand mix** in a single ASG — critical for cost optimization.
- Supports placement groups, Capacity Reservations, Dedicated Hosts, and T2/T3 unlimited burst mode.
- Can launch individual instances directly (`aws ec2 run-instances --launch-template`) — not just ASGs.

| | Launch Configuration | Launch Template |
|---|---|---|
| **Mutable** | No — recreate for any change | Yes — add a new version |
| **Versioning** | No | Yes |
| **Mixed instances / Spot + OD mix** | No | Yes |
| **Direct instance launch** | No | Yes |
| **AWS status** | Legacy (no new features) | Recommended |
