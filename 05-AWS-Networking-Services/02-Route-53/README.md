## Route 53
Amazon Route 53 is a highly available and scalable Domain Name System (DNS) web service designed to route end users to Internet applications. Route 53 is designed to connect user requests to infrastructure running in AWS (like EC2 instances, load balancers, or S3 buckets) or external resources. It offers
- Domain Registration: Register domain names directly through Route 53.
- DNS Service: Route 53 translates friendly domain names like www.example.com into IP addresses like 192.0.2.1 or AWS resources like EC2.
- Health Checking: Monitor the health of your application and route traffic only to healthy endpoints.

**DNS Record Types:**
- **A Record**: Maps a domain name to an IPv4 address.
- **AAAA Record**: Maps a domain name to an IPv6 address.
- **CNAME Record**: Maps a domain name to another domain name (canonical name). It cannot be used for the root domain.(sub domain is allowed).
- **MX Record**: Specifies the mail server responsible for receiving email on behalf of a domain.
- **TXT Record**: Allows you to associate arbitrary text with a domain name. Commonly used for verification purposes (e.g., Google Search Console, SPF records).

**DNS Routing Policies:**
- **Simple Routing**: Basic routing policy that routes traffic to a single resource.
- **Weighted Routing**: Distributes traffic across multiple resources based on assigned weights. Useful for A/B testing or gradual migrations.(e.g., 70% to one resource and 30% to another).
- **Latency-based Routing**: Routes traffic to the resource with the lowest latency for the user. Useful for multi-region applications.
- **Geolocation Routing**: Routes traffic based on the geographic location of the user. Useful for serving different content to users in different regions.
- **Geoproximity Routing**: Routes traffic based on the geographic location of the user and resources, with optional bias. Useful for directing traffic to resources closer to the user.
- **Failover Routing**: Routes traffic to a primary resource unless it becomes unhealthy, in which case it routes to a secondary resource.
- **Multivalue Answer Routing**: Returns multiple values (e.g., IP addresses) for a single DNS query. Useful for load balancing and redundancy.

### Interview Questions:
1. **Difference between A and CNAME records?**
   - A record maps a domain name to an IP address, while a CNAME record maps a domain name to another domain name. CNAME cannot be used for the root domain.

2. **What is Alias record?**
   - An Alias record is a Route 53-specific extension to DNS that allows you to map a domain name to an AWS resource (like an S3 bucket or CloudFront distribution) without needing to know the IP address. It can be used at the root domain level, unlike CNAME records.
   - Alias records are automatically updated if the underlying resource changes its IP address.

3. **What is Hosted Zone?**
   - A hosted zone is a container for records that define how you want to route traffic for a domain and its subdomains. It can be public (accessible from the internet) or private (accessible only within an Amazon VPC).
    - Each hosted zone contains a set of DNS records that define how to route traffic for the domain and its subdomains.

4. **Difference between Geolocation and Geoproximity routing?**
   - Geolocation routing routes traffic based on the geographic location of the user, while Geoproximity routing routes traffic based on the geographic location of the user and resources, with optional bias. 
   - Geoproximity routing allows you to control how much traffic is routed to each resource based on its proximity to the user.
   - Geolocation routing is based solely on the user's location, while Geoproximity routing can be adjusted to favor certain resources over others.

5. **How Route 53 integrates with CloudFront?**
   - Route 53 can be used to create a custom domain name for a CloudFront distribution. You can create an Alias record in Route 53 that points to the CloudFront distribution's domain name.
   - This allows you to use your own domain name (e.g., www.example.com) instead of the default CloudFront domain name (e.g., d1234567890.cloudfront.net).
   - Route 53 also provides DNS failover capabilities, allowing you to route traffic to a backup CloudFront distribution if the primary one becomes unavailable.
