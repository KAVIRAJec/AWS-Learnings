## EC2 Instance Types

EC2 instances are categorized into different families based on their use cases.
- **General Purpose**: Balanced compute, memory, and networking resources. Examples: T3, M5, M6g.(Application: web servers, code repositories, etc.)
- **Compute Optimized**: High-performance processors for compute-intensive tasks. Examples: C5, C6g.(Application: Game servers, etc.)
- **Memory Optimized**: Designed for memory-intensive applications. Examples: R5, R6g.(Application: high-performance databases, in-memory analytics etc.)
- **Storage Optimized**: High disk throughput and IOPS for data-intensive applications. Examples: I3, D2.(Application: NoSQL databases, data warehousing, etc.)
- **Accelerated/GPU Intensive Computing**: GPU-based instances for machine learning and graphics-intensive applications. Examples: P3, G4.(Application: Machine learning, 3D rendering, etc.)

---

## EC2 Pricing Models
- **On-Demand Instances**: Pay-per-use pricing for instances that are launched and terminated as needed. Based on the number of seconds/hours the instance is running.
- **Reserved Instances**: Commit to a one or three-year term for discounted pricing. It is kind of booking an instance for a specific period.(Applies discount upto 70%)
- **Spot Instances**: Purchase unused EC2 capacity at reduced rates. Ideal for short-term, flexible workloads. Spot instances can be interrupted by AWS with two-minute warning if the capacity is needed elsewhere.(Applies discount upto 90%)
- No pricing for Data Transfer In bound & between other services within same region, but Data Transfer Out is charged based on the amount of data transferred out of AWS.

---

## EC2 Reserved Instances
- **Standard Reserved Instances**: Provide a significant discount (up to 72%) compared to On-Demand pricing. They are best for steady-state workloads and can be modified to change the Availability Zone, instance type, or network platform.
- **Convertible Reserved Instances**: Allow you to change the instance type, operating system, and tenancy over the term of the reservation, providing flexibility to adapt to changing needs. Provides a discount of up to 55% compared to On-Demand pricing.
- **Scheduled Reserved Instances**: Allow you to reserve capacity for specific time periods, such as daily or weekly, providing flexibility for workloads that require predictable capacity at specific times.

---

## EC2 Saving Plans
Saving Plans are flexible pricing models that provide significant savings on your AWS compute usage. They offer a flexible pricing model that allows you to save up to 72%(Same as Reserved Instances) on your AWS compute costs compared to On-Demand pricing. There are two types of Saving Plans:
- **Compute Savings Plans**: Provide the most flexibility and apply to any EC2 instance regardless of region, instance family, operating system, or tenancy. They offer savings of up to 66% compared to On-Demand pricing.
- **EC2 Instance Savings Plans**: Provide the most savings when you commit to a specific instance family within a region, offering savings of up to 72% compared to On-Demand pricing.
![alt text](image-2.png)

---

## EC2 Spot Instances
Spot Instances are a cost-effective way to run applications on AWS by taking advantage of unused EC2 capacity. They allow you to bid on spare EC2 capacity at significantly lower prices(upto 90%) than On-Demand instances. Spot Instances can be interrupted by AWS with two-minute warning if the capacity is needed elsewhere.
- **Spot Fleet**: A collection of Spot Instances and optional On-Demand instances that are launched and managed as a single unit. Spot Fleet allows you to specify the target capacity, instance types, and pricing strategy for your Spot Instances.
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
Capacity Reservations allow you to reserve capacity for your EC2 instances in a specific Availability Zone. This ensures that you have the capacity you need when you need it, even during peak demand periods. Capacity Reservations are charged at the same rate as On-Demand instances even it is not used and can be used in conjunction with other pricing models, such as Reserved Instances and Spot Instances. It does not provide any discount on the pricing.
