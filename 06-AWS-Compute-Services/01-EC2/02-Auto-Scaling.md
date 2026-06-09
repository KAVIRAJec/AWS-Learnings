## EC2 Auto Scaling

## EC2 Auto Scaling Group (ASG)

Automatically adds or removes EC2 instances based on demand. Free service — you only pay for the instances it creates.

**Three core settings:**
- **Minimum** — ASG will never go below this count, even under zero load.
- **Desired** — the number ASG tries to maintain at all times.
- **Maximum** — ASG will never exceed this count, even under peak load.

---

### Scaling Types

**1. Dynamic Scaling** — reacts to what's happening right now (CloudWatch metrics)

- **Target Tracking**: You pick a target metric value — ASG automatically adds/removes instances to maintain it.
  - Example: "Keep average CPU at 50%" → if CPU rises to 70%, ASG scales out; if it drops to 30%, ASG scales in.
  - Simplest to configure — recommended default.

- **Step Scaling**: You define CloudWatch alarms with multiple steps — different actions at different thresholds.
  - Example: CPU 60–80% → add 1 instance. CPU >80% → add 3 instances.
  - More control than Target Tracking for sudden, large traffic spikes.

- **Simple Scaling**: Single CloudWatch alarm → single fixed action. After the action, waits for cooldown before doing anything else.
  - Older, less flexible than Step Scaling — rarely preferred today.

**2. Scheduled Scaling** — reacts to a known future event (time-based)
- You set a specific time to scale out or in.
- Example: "Every weekday at 8AM add 5 instances, at 10PM remove them."
- Best for predictable, recurring traffic patterns.

**3. Predictive Scaling** — reacts before the load hits (ML-based forecast)
- AWS analyses historical CloudWatch data and predicts future demand.
- Pre-launches instances before the load spike arrives — no lag waiting for a metric to breach a threshold.
- Example: Traffic always spikes on Monday morning → ASG scales out Sunday night automatically.
- Best combined with Dynamic Scaling (predictive handles the forecast, dynamic handles unexpected deviations).

---

| Type | Trigger | Best for |
|---|---|---|
| Target Tracking | Metric drifts from target | General purpose — easiest setup |
| Step Scaling | Alarm threshold crossed | Workloads needing graduated response |
| Simple Scaling | Alarm threshold crossed | Simple single-step reactions |
| Scheduled | Time of day / date | Known recurring patterns |
| Predictive | ML forecast of future demand | Recurring spikes with historical data |

---

**Scaling Cooldown** — after any scaling action, ASG pauses for **300 seconds (default)** before triggering another. Prevents thrashing — gives newly launched instances time to start handling traffic before ASG decides more are needed.

**Instance Warm-Up** — time (in seconds) given to a newly launched instance to warm up before it is counted in Auto Scaling metrics.
- Used with **Target Tracking** and **Step Scaling** policies.
- During warm-up, the instance is **not counted toward the group's current capacity** for scaling metric calculations — prevents ASG from triggering another scale-out before the new instance is actually serving traffic.
- **Warm-up vs Cooldown**: Warm-up applies to the individual new instance (excluded from metrics until ready); cooldown applies to the entire ASG (pauses all scaling actions after a scale event).

| | Instance Warm-Up | Scaling Cooldown |
|---|---|---|
| **Applies to** | Individual new instance | Entire ASG |
| **Effect** | Exclude instance from metric until ready | Pause all scaling actions |
| **Used with** | Target Tracking, Step Scaling | Simple Scaling (default), all policies |
| **Purpose** | Prevent premature scale-out triggered by a still-booting instance | Prevent rapid consecutive scaling actions (thrashing) |

### Rebalancing

ASG always tries to keep instances **evenly spread across AZs**. Rebalancing is triggered when the balance breaks:

| Trigger | Why it Breaks Balance |
|---|---|
| AZ recovered after outage | ASG had concentrated instances in remaining AZs |
| Instances manually terminated | One AZ now has fewer instances |
| New AZ added to ASG | Existing instances are all in old AZs |
| Spot Capacity Rebalancing | Spot instance at high interruption risk — proactive replacement |

**How rebalancing works — Launch first, terminate second:**
```
Before:  AZ-A [3]   AZ-B [1]   ← imbalanced

Step 1:  AZ-A [3]   AZ-B [2]   ← launch in underrepresented AZ first
Step 2:  AZ-A [2]   AZ-B [2]   ← then terminate from overrepresented AZ

Result:  balanced, capacity never drops below desired
```
> ASG launches the replacement **before** terminating the old instance — so you temporarily exceed the max capacity by 10% during rebalancing. This is intentional.

---

### Scaling Activity — Termination Policy (Scale In)

When ASG scales **in** (removes instances), AWS runs through a fixed evaluation order before picking which instance to kill. Each step narrows the candidate pool — the next step only applies if there's still a tie.

**Default Termination Order:**

```
1. Skip instances with Scale-In Protection enabled
         │
         ▼
2. Pick the AZ with the MOST instances
   (keeps instances balanced across AZs)
         │
         ▼
3. Within that AZ — find instances using the OLDEST Launch Template or Launch Configuration
   → If one instance uses a Launch Configuration and another uses a Launch Template,
     the Launch Configuration instance is always terminated first — LC is always
     considered older than any LT version, regardless of actual creation date.
         │
         ▼
4. pick the instance CLOSEST to the next full billing hour
   (avoids paying for an unused partial hour)
         │
         ▼
5. pick at RANDOM
         │
         ▼
      TERMINATE
```

> Steps 3 → 4 → 5 are the tie-breakers applied within the AZ selected in step 2.

**Built-in termination policies** (choose one or chain them):

| Policy | Behaviour |
|---|---|
| `Default` | Full order above (AZ balance → oldest template → billing hour → random) |
| `OldestInstance` | Always removes the oldest running instance regardless of AZ |
| `NewestInstance` | Removes the newest — useful to roll back a bad deployment |
| `OldestLaunchTemplate` | Removes instances using the oldest launch template version |
| `ClosestToNextInstanceHour` | Maximise cost savings by removing instances about to be billed |
| `AllocationStrategy` | Optimise mix of On-Demand + Spot (used with mixed instance groups) |

> **Scale-In Protection** — you can mark individual instances as protected. ASG will never terminate them during scale-in, regardless of the policy. Useful for instances running long-running jobs.

---

### Instance Refresh

When you update a **Launch Template** (new AMI, new user data, etc.) and want existing running instances to pick up the change — use **Instance Refresh** instead of manually terminating them.

```
Updated Launch Template v2
         │
         ▼
Instance Refresh starts
         │
  ┌──────▼──────────────────────────────┐
  │  Batch 1: terminate old → launch new │  ← waits for new instance to pass health check
  │  Batch 2: terminate old → launch new │
  │  Batch N: ...                        │
  └──────────────────────────────────────┘
         │
         ▼
All instances now running on Launch Template v2
```

**Key settings:**
- **Min Healthy %** — percentage of fleet that must stay healthy during the refresh (e.g., 90% = replace only 10% at a time).
- **Instance Warmup** — seconds to wait after launch before counting the new instance as healthy and moving to the next batch.
- If a new instance fails health checks → refresh pauses automatically.

---

### ASG Instance Lifecycle States

Every instance in an Auto Scaling Group moves through a defined lifecycle. Understanding states helps debug scaling issues and unexpected terminations.

```
          Scale Out triggered
                │
                ▼
           [ Pending ]  ← instance is being launched, lifecycle hooks run here
                │
                ▼
          [ InService ] ← instance is healthy, receiving traffic from LB
         ╱              ╲
        ╱                ╲
[ EnteringStandby ]    Scale In triggered / Unhealthy
        │                      │
        ▼                      ▼
    [ Standby ]          [ Terminating ] ← lifecycle hooks run here
        │                      │
  (back to InService)          ▼
                          [ Terminated ]
```

| State | What it means |
|---|---|
| **Pending** | Instance is launching — not yet serving traffic. Lifecycle hooks can run here (e.g., warm up, register with config service). |
| **InService** | Instance is healthy and active. Included in load balancer target group. Counts toward desired capacity. |
| **EnteringStandby** | Transition state before Standby — connection draining happens here. |
| **Standby** | Instance is still running but **removed from load balancer** and excluded from health checks. Use this for maintenance (patching, debugging) without terminating. Does **not** trigger a replacement. |
| **Terminating** | Instance is being terminated — lifecycle hooks can run here (e.g., drain jobs, deregister from service discovery). |
| **Terminated** | Instance is gone. ASG launches a replacement if below desired capacity. |
| **Detaching** | Instance is being removed from the ASG permanently (not terminated — becomes a standalone EC2). |
| **Detached** | Instance is fully removed from ASG and running independently. |

> **Standby vs Detach** — Standby keeps the instance inside the ASG (counts toward capacity, can return to InService). Detach removes it from the ASG permanently.

---

### ASG Processes

ASG processes are background activities the Auto Scaling Group performs. Each can be **suspended individually** — useful for maintenance or debugging without dismantling the ASG.

| Process | What it does | Why you'd suspend it |
|---|---|---|
| **Launch** | Adds new instances when scaling out or replacing unhealthy ones | Temporarily stop new launches during maintenance |
| **Terminate** | Removes instances when scaling in | Prevent accidental termination during debugging |
| **HealthCheck** | Monitors instance health via EC2 status checks or ELB health checks | Suspend to stop replacements while patching |
| **ReplaceUnhealthy** | Terminates unhealthy instances and launches replacements | Suspend to investigate a failing instance before it gets replaced |
| **AZRebalance** | Keeps instances spread evenly across AZs | Suspend if you temporarily want uneven distribution |
| **AlarmNotification** | Receives CloudWatch alarm signals and triggers dynamic scaling policies | Suspend to pause auto scaling reactions to metrics |
| **ScheduledActions** | Executes scheduled scaling actions (scale out at 9AM, scale in at 6PM) | Suspend to hold capacity constant during a deployment |
| **AddToLoadBalancer** | Registers new instances with the load balancer target group on launch | Suspend during blue/green switchover |
| **InstanceRefresh** | Manages rolling replacement of instances for Launch Template updates | Suspend to pause a rolling refresh mid-way |

> Suspending **Launch** + **Terminate** together effectively freezes the ASG — no new instances added, none removed.

---

### Why ASG Is Not Terminating an Unhealthy Instance

An instance is marked unhealthy but ASG hasn't replaced it yet — here are all the reasons why:

| Reason | What's happening |
|---|---|
| **Health Check Grace Period not expired** | After launch, ASG ignores health check failures for the grace period duration (default 300s). Instance may still be booting or initializing. |
| **Scale-In Protection enabled** | The instance is marked as protected — ASG will never terminate it during scale-in or unhealthy replacement. |
| **`ReplaceUnhealthy` process is suspended** | ASG detects unhealthy instances but won't act on them while this process is suspended. |
| **`HealthCheck` process is suspended** | ASG isn't even evaluating health — it can't know the instance is unhealthy. |
| **Instance is in Standby state** | ASG does not run health checks on Standby instances — they are intentionally excluded. |
| **Health check type mismatch** | ASG is configured with EC2 health checks only, but the instance is unhealthy at the ELB level. ELB health check results are only used if explicitly enabled on the ASG. |
| **ASG at minimum capacity, replacement launch failing** | ASG tries to launch a replacement first before terminating the unhealthy one. If the replacement launch fails (e.g., capacity constraints, subnet issues), the unhealthy instance stays until a launch succeeds. |
| **Lifecycle hook blocking termination** | A `autoscaling:EC2_INSTANCE_TERMINATING` lifecycle hook is pending — the instance stays in `Terminating:Wait` until the hook completes or times out. |
| **Connection draining in progress** | ELB is still draining active connections from the instance before deregistering it — termination is held until draining completes or the timeout expires. |

---

### EC2 Instance Status Checks & Impaired Status

EC2 runs two automatic status checks every minute on every instance:

| Check | What it tests | Who fixes it |
|---|---|---|
| **System Status Check** | Underlying AWS hardware/network — power, host connectivity, hypervisor | AWS must intervene (hardware failure, host issue) |
| **Instance Status Check** | OS-level issues — kernel, network config, memory, corrupted filesystem | You must fix (reboot, patch, reconfigure) |

When either check fails, the instance is marked **Impaired**.

**CloudWatch Alarm Actions per Check Type:**

| | System Status Check failure | Instance Status Check failure |
|---|---|---|
| **Root cause** | Bad hardware/host (AWS side) | OS crash, kernel issue, bad config (your side) |
| **CloudWatch metric** | `StatusCheckFailed_System` | `StatusCheckFailed_Instance` |
| **Alarm action** | **Recover** — migrate to new hardware | **Reboot** — restart on same hardware |
| **Hardware after action** | New underlying host | Same underlying host |
| **EBS data** | Preserved (EBS is network-attached, not tied to host) | Preserved |
| **RAM data** | Lost (reboot happens during migration) | Lost (it's a reboot) |
| **Instance ID / IPs / metadata** | All preserved | All preserved |

Both alarm actions preserve your EBS data and lose RAM — the only difference is **System recovery moves to new hardware**, while **Instance reboot stays on the same hardware**.

**Constraints:**
- **Terminated instances cannot be recovered.**
- Automated recovery only works on instance types that support it (most modern types do).

---

- There are 2 main types of scaling the instances:
  - **Vertical Scaling(Scale Up/Down)**: Increasing or decreasing the size of an instance (e.g., RAM, CPU, etc,.) to meet demand. This is not fault-tolerant and does not provide high availability.(Scale up: Increasing the size of an instance, Scale down: Decreasing the size of an instance)
  - **Horizontal Scaling(Scale Out/In)**: Adding or removing instances to meet demand. This is fault-tolerant and provides high availability.(Scale out: Adding more instances, Scale in: Removing instances)
