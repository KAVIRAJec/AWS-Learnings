## EC2 Instance Types

EC2 instances are categorized into different families based on their use cases.
- **General Purpose**: Balanced compute, memory, and networking resources. Examples: T3, M5, M6g.(Application: web servers, code repositories, etc.)
- **Compute Optimized**: High-performance processors for compute-intensive tasks. Examples: C5, C6g.(Application: Game servers, etc.)
- **Memory Optimized**: Designed for memory-intensive applications. Examples: R5, R6g.(Application: high-performance databases, in-memory analytics etc.)
- **Storage Optimized**: High disk throughput and IOPS for data-intensive applications. Examples: I3, D2.(Application: NoSQL databases, data warehousing, etc.)
- **Accelerated/GPU Intensive Computing**: GPU-based instances for machine learning and graphics-intensive applications. Examples: P3, G4.(Application: Machine learning, 3D rendering, etc.)

---

## EC2 Instance Lifecycle & Billing

EC2 instances transition through these states:

| State | Description |
|---|---|
| **pending** | Launching or restarting — not yet running |
| **running** | Active and ready for use |
| **stopping** | Preparing to stop or hibernate |
| **stopped** | Shut down, can be restarted |
| **shutting-down** | Preparing to terminate |
| **terminated** | Permanently deleted, cannot be restarted |

**Billing per state — what you are charged for:**

| State | On-Demand | Spot | Reserved |
|---|---|---|---|
| **pending** | No | No | Yes (term runs) |
| **running** | Yes | Yes | Yes |
| **stopping** (preparing to stop) | No | No | Yes (term runs) |
| **stopping** (preparing to hibernate) | **Yes** | No | Yes (term runs) |
| **stopped** | No | No | Yes (term runs) |
| **terminated** | No | No | **Yes — billed until end of term** |

**Key rules to remember:**
- **pending** = not billed for On-Demand or Spot — instance hasn't started yet.
- **stopping to stop** = not billed — instance is shutting down compute.
- **stopping to hibernate** = **still billed** — instance RAM is being saved to EBS, compute is still active.
- **Reserved Instances on terminated instances** = **still billed until the end of the reserved term** regardless of instance state — you committed to the term, not the instance.

---

## EC2 Pricing Models
- **On-Demand Instances**: Pay-per-use pricing for instances that are launched and terminated as needed. Based on the number of seconds/hours the instance is running.
- **Reserved Instances**: Commit to a one or three-year term for discounted pricing. It is kind of booking an instance for a specific period.(Applies discount upto 70%)
- **Spot Instances**: Purchase unused EC2 capacity at reduced rates. Ideal for short-term, flexible workloads. Spot instances can be interrupted by AWS with two-minute warning if the capacity is needed elsewhere.(Applies discount upto 90%)
- No pricing for Data Transfer In bound & between other services within same region, but Data Transfer Out is charged based on the amount of data transferred out of AWS.

**EC2 On-Demand Instance Limits (vCPU-based):**
- AWS enforces a **vCPU-based limit on On-Demand instances per region** — not per Availability Zone, not per instance count.
- The limit is measured in **total vCPUs** across all running On-Demand instances in the region (e.g., launching 50 × 4-vCPU instances = 200 vCPUs consumed).
- New AWS accounts start with lower limits by default.
- If you hit the limit, subsequent launch requests fail — switching to a different AZ within the same region does **not** help since the limit is region-wide.
- **Resolution**: Submit a **Service Quotas limit increase request** to AWS for the specific region, then retry once approved.

| Limit type | Scope | Can be increased? |
|---|---|---|
| vCPU-based On-Demand limit | Per region | Yes — via Service Quotas |
| Reserved Instances | 20 per region (legacy count-based) | Yes — via limit increase |
| Spot Instances | Dynamic per region | Yes — via limit increase |

---

## EC2 Reserved Instances
- **Standard Reserved Instances**: Provide a significant discount (up to 72%) compared to On-Demand pricing. They are best for steady-state workloads and can be modified to change the Availability Zone, instance type, or network platform.
- **Convertible Reserved Instances**: Allow you to change the instance type, operating system, and tenancy over the term of the reservation, providing flexibility to adapt to changing needs. Provides a discount of up to 55% compared to On-Demand pricing.
- **Scheduled Reserved Instances**: Allow you to reserve capacity for specific time periods, such as daily or weekly, providing flexibility for workloads that require predictable capacity at specific times.

**Zonal vs Regional Reserved Instances — key distinction:**

| | Zonal Reserved Instance | Regional Reserved Instance |
|---|---|---|
| **Scope** | Specific AZ | Entire region (any AZ) |
| **Capacity reservation** | **Yes** — guarantees capacity in that AZ | **No** — billing discount only, no capacity guarantee |
| **Billing discount** | Yes | Yes |
| **AZ flexibility** | Fixed to one AZ | Applies to any AZ in the region |
| **Instance size flexibility** | No | Yes (within instance family) |

> **Regional RIs do NOT reserve capacity** — they only provide billing discounts. If you need guaranteed capacity in a specific AZ, use a **Zonal RI** or an **On-Demand Capacity Reservation**.

---

## EC2 Saving Plans
Saving Plans are flexible pricing models that provide significant savings on your AWS compute usage. They offer a flexible pricing model that allows you to save up to 72%(Same as Reserved Instances) on your AWS compute costs compared to On-Demand pricing. There are two types of Saving Plans:
- **Compute Savings Plans**: Provide the most flexibility and apply to any EC2 instance regardless of region, instance family, operating system, or tenancy. They offer savings of up to 66% compared to On-Demand pricing.
- **EC2 Instance Savings Plans**: Provide the most savings when you commit to a specific instance family within a region, offering savings of up to 72% compared to On-Demand pricing.
![alt text](image-2.png)

---

## EC2 Spot Instances

Spot Instances are **unused EC2 capacity** that AWS sells at up to 90% discount compared to On-Demand. AWS sets the **Spot price** per instance type per AZ and adjusts it gradually based on supply and demand.

**How it works — the core idea:**
```
AWS data center always has unused capacity sitting idle.
AWS sells that idle capacity cheap → you get a Spot Instance.
When AWS needs that capacity back (demand rises) → AWS interrupts your instance.
```

You don't bid anymore (old model). You just specify a **max price** (optional — defaults to On-Demand price). As long as the current Spot price ≤ your max price, your instance runs.

---

### Interruption

When AWS needs the capacity back, it sends a **2-minute warning** before terminating your Spot Instance. At interruption(after 2 minutes), AWS can:
- **Terminate** (default) — instance is stopped and deleted.
- **Stop** — instance state saved to EBS (if EBS-backed).
- **Hibernate** — RAM state saved to EBS, resumes exactly where it left off.

You choose the interruption behavior when making the Spot request.

> Spot Instances are **not suitable** for workloads that cannot tolerate interruption — databases, critical APIs, long transactions. Best for: batch jobs, data processing, CI/CD, ML training, rendering.

---

### One-Time vs Persistent Request

| | One-Time | Persistent |
|---|---|---|
| **On fulfillment** | Instance launches once | Instance launches whenever capacity is available |
| **On interruption** | Request closes, instance gone | Request **reopens automatically** — new instance launched when capacity returns |
| **On manual stop** | N/A | Request stays open but does **not** relaunch until you manually start the instance again |
| **Use case** | Single batch job | Long-running workload that can pause and resume |

**Flow — Persistent request:**
```
You submit persistent Spot request
        │
        ▼
Capacity available → Instance launches (request = active)
        │
        ▼
AWS needs capacity → 2-min warning → Instance interrupted (request = open again)
        │
        ▼
Capacity available again → New instance launched automatically
```

**To fully cancel a persistent Spot request:** cancel the request first, then terminate the instance. If you only terminate the instance, the persistent request will just relaunch a new one.

---

### Spot Fleet

A **Spot Fleet** is a collection of Spot Instances (and optionally On-Demand instances) managed as a single unit to meet a target capacity.

- You define **multiple instance pools** (type + AZ combinations) — Spot Fleet picks from them based on your strategy.
- **Allocation strategies:**
  - `lowestPrice` — launch from the cheapest pool (cost-optimized but higher interruption risk).
  - `diversified` — spread across all pools (reduces interruption impact).
  - `capacityOptimized` — launch from the pool with most available capacity (lowest interruption risk).
  - `priceCapacityOptimized` — picks pools with high capacity AND low price (recommended default).
- Spot Fleet automatically replaces interrupted instances to maintain target capacity.

![alt text](image-1.png)

---

## EC2 Dedicated Hosts *(Most expensive)*
Dedicated Hosts are physical servers dedicated to your use, providing control over how instances are placed on the server. They allow you to use your existing server-bound software licenses and provide visibility into the number of sockets, cores, and host ID.**We can use Dedicated Hosts to meet compliance requirements for certain workloads. Also we can create multiple instances as needed.**
 - **On Demand Dedicated Hosts**: Pay per second for the Dedicated Host while it is active.
   - **Reserved Dedicated Hosts**: Commit to a one or three-year term for discounted pricing. It is kind of booking a Dedicated Host for a specific period.

---

## EC2 Dedicated Instances
Dedicated Instances are EC2 instances that run on hardware dedicated to a single customer. They provide the same level of isolation as Dedicated Hosts but do not provide visibility into the underlying hardware. **Dedicated Instances are charged at a higher rate than standard EC2 instances.**

---

## EC2 Capacity Reservations

On-Demand Capacity Reservations let you reserve EC2 compute capacity in a **specific AZ** for any duration — **without a 1 or 3-year term commitment**.

- Capacity is available immediately after creation and stays reserved until you cancel it.
- **Billing starts as soon as the reservation is active** — you are charged at On-Demand rates whether or not you run instances against the reservation.
- **No billing discount** on their own — but you can combine them with **Savings Plans** or **Regional Reserved Instances** to get discounts on the reserved capacity.
- **Cancel at any time** — no penalties, unlike Reserved Instances.
- You specify: AZ, instance type, number of instances, platform/OS.

**Use case**: Workloads that run on a recurring schedule (e.g., nightly batch jobs from 10 PM–3 AM) — create the reservation before the window, cancel after. Ensures capacity without paying for a full-year commitment.

| | On-Demand Capacity Reservation | Zonal Reserved Instance |
|---|---|---|
| **Term commitment** | None — cancel anytime | 1 or 3 years |
| **Capacity guarantee in AZ** | Yes | Yes |
| **Billing discount** | No (combine with Savings Plan/Regional RI) | Yes |
| **Best for** | Short or recurring capacity needs | Long-running, steady-state workloads |
