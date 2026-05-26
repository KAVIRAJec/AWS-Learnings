## EC2 Auto Scaling

- **Auto Scaling**: Automatically adjusts the number of EC2 instances in a group based on demand. It ensures that you have the right number of instances running to handle the load. Auto Scaling can be configured to scale in (remove instances) or scale out (add instances) based on predefined policies, schedules, or health checks. It is free service, only pay for resources created by Auto Scaling.
- There are 3 types of scaling:
  - **Dynamic Scaling**: Automatically adjusts the number of instances in response to changing demand. It can scale in or out based on CloudWatch metrics, such as CPU utilization or network traffic.
      - **Target Tracking Scaling**: Automatically adjusts the number of instances to maintain a specified target value for a specific metric, such as CPU utilization or request count.
      - **Simple/Step Scaling**: Allows you to define scaling policies based on CloudWatch alarms. You can specify the number of instances to add or remove based on the alarm state (e.g., CPU utilization above a certain threshold). **Step scaling** allows you to define multiple steps for scaling actions based on different alarm states. **Simple scaling** allows you to define a single scaling action for each alarm state.
  - **Scheduled Scaling**: Allows you to set a schedule for scaling actions based on predictable changes in demand. For example, you can schedule scaling actions to occur at specific times of the day or week.
  - **Predictive Scaling**: Uses machine learning to predict future demand and automatically adjusts the number of instances in advance. It can help ensure that you have the right number of instances running to handle expected traffic spikes or drops.

- **Scaling Cooldown**: After a scaling activity is triggered, there is a cooldown period during which no further scaling activities will be initiated. This helps prevent rapid fluctuations in the number of instances and allows the system to stabilize after a scaling action. The default cooldown period is 300 seconds (5 minutes), but it can be configured.

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

- There are 2 main types of scaling the instances:
  - **Vertical Scaling(Scale Up/Down)**: Increasing or decreasing the size of an instance (e.g., RAM, CPU, etc,.) to meet demand. This is not fault-tolerant and does not provide high availability.(Scale up: Increasing the size of an instance, Scale down: Decreasing the size of an instance)
  - **Horizontal Scaling(Scale Out/In)**: Adding or removing instances to meet demand. This is fault-tolerant and provides high availability.(Scale out: Adding more instances, Scale in: Removing instances)
