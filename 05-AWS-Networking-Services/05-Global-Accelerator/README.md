## AWS Global Accelerator

AWS Global Accelerator is a networking service that improves the **availability and performance** of applications for global users by routing traffic through the **AWS global network** instead of the public internet.

**Unicast vs Anycast:**
- **Unicast**: One server holds one IP address — traffic is sent to a specific server. Standard routing on the internet.
- **Anycast**: Multiple servers share the same IP address — traffic is automatically routed to the **nearest/best server**. Global Accelerator uses anycast to route users to the closest AWS edge location.

**Key Concepts:**
- **Static Anycast IPs**: Provides **2 static IPs** (anycast) as a single fixed entry point — IPs never change even as you add/remove regions, AZs, or endpoints behind them. No DNS updates or client-side changes needed when your backend topology changes.
- **Edge Locations**: User traffic enters the AWS network at the nearest edge location and travels over AWS's private backbone — avoids slow/unpredictable public internet hops.
- **Endpoints**: Supports ALB, NLB, EC2 instances, and Elastic IPs across one or more AWS regions. You can move endpoints between AZs or Regions without updating DNS or client applications — the 2 static IPs remain the same.
- **Health Checks**: Continuously monitors endpoint health — if an endpoint fails, Global Accelerator **automatically redirects new connections to a healthy endpoint within seconds** (no DNS TTL wait, unlike Route 53 failover).
- **Traffic Dials** (per endpoint group): Control the **percentage of traffic** routed to each region's endpoint group — e.g., dial us-east-1 to 10% and eu-west-1 to 90% for gradual regional rollouts or performance testing.
- **Endpoint Weights** (per endpoint within a group): Control the **proportion of traffic** directed to each individual endpoint within an endpoint group — e.g., send 80% to ALB-1 and 20% to ALB-2 in the same region.
- **DDoS Protection**: Integrated with **AWS Shield Standard** by default.

```
Traffic Dial  → controls % traffic going to a Region (endpoint group level)
Endpoint Weight → controls % traffic split within a Region (endpoint level)

Global Accelerator
  ├── Endpoint Group: us-east-1  [Traffic Dial: 80%]
  │       ├── ALB-1  [Weight: 80]
  │       └── ALB-2  [Weight: 20]
  └── Endpoint Group: eu-west-1  [Traffic Dial: 20%]
          └── ALB-3  [Weight: 100]
```

**CloudFront vs Global Accelerator:**
- Both use AWS edge locations and integrate with Shield.
- **CloudFront**: Caches content at the edge — best for static/cacheable content (HTTP).
- **Global Accelerator**: No caching — proxies traffic to the origin over AWS backbone. Best for non-HTTP (TCP/UDP), gaming, IoT, VoIP, or apps needing static IPs.

**Use cases:** Global applications needing low latency, multi-region failover, or static IPs for whitelisting.
