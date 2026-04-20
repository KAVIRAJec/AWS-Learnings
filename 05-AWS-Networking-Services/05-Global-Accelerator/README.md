## AWS Global Accelerator

AWS Global Accelerator is a networking service that improves the **availability and performance** of applications for global users by routing traffic through the **AWS global network** instead of the public internet.

**Unicast vs Anycast:**
- **Unicast**: One server holds one IP address — traffic is sent to a specific server. Standard routing on the internet.
- **Anycast**: Multiple servers share the same IP address — traffic is automatically routed to the **nearest/best server**. Global Accelerator uses anycast to route users to the closest AWS edge location.

**Key Concepts:**
- **Static Anycast IPs**: Provides **2 static IPs** (anycast) as a fixed entry point to your application — IPs don't change even if backend endpoints change.
- **Edge Locations**: User traffic enters the AWS network at the nearest edge location and travels over AWS's private backbone — avoids slow/unpredictable public internet hops.
- **Endpoints**: Supports ALB, NLB, EC2 instances, and Elastic IPs across one or more AWS regions.
- **Health Checks**: Continuously monitors endpoint health and automatically reroutes traffic to healthy endpoints.
- **Traffic Dials**: Control the percentage of traffic sent to each endpoint group (useful for blue/green deployments or gradual rollouts).
- **DDoS Protection**: Integrated with **AWS Shield Standard** by default.

**CloudFront vs Global Accelerator:**
- Both use AWS edge locations and integrate with Shield.
- **CloudFront**: Caches content at the edge — best for static/cacheable content (HTTP).
- **Global Accelerator**: No caching — proxies traffic to the origin over AWS backbone. Best for non-HTTP (TCP/UDP), gaming, IoT, VoIP, or apps needing static IPs.

**Use cases:** Global applications needing low latency, multi-region failover, or static IPs for whitelisting.
