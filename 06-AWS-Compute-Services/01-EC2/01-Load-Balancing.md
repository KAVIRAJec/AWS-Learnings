## EC2 Load Balancing

- **Elastic Load Balancing (ELB)**: Automatically distributes incoming application traffic across multiple targets, such as EC2 instances, ECS containers, Lambda and IP addresses(private). It helps ensure high availability and fault tolerance for your applications.
- **Benefits of Load Balancing**:
  - Distribute Traffic
  - Improve Fault Tolerance
  - Increase Availability
  - Health Checks(Protocols: HTTP, Port: 4567, Endpoint: /health)
  - SSL Termination
  - Single point of access for clients
  - Can traffic to multiple target groups (e.g., multiple applications) using a single load balancer.
- **Types of Load Balancers**:
  - **Application Load Balancer (ALB)**: Operates at the application layer (Layer 7) and is ideal for HTTP/HTTPS traffic. It can route requests based on content and supports WebSocket and HTTP/2. Supports Path-based routing, host-based routing, and query string parameter routing. It is suitable for microservices and containerized applications. ALB IPs are **dynamic** — always use the AWS-provided DNS name (e.g., `my-alb-123.us-east-1.elb.amazonaws.com`), never hardcode IPs.
  - Instance can get the IP of the Client using the X-Forwarded-For header, Port: X-Forwarded-Port header, Protocol: X-Forwarded-Proto header.
  - **Network Load Balancer (NLB)**: Operates at the transport layer (Layer 4) and is designed for high-performance TCP/UDP & TLS traffic. **It is suitable for applications with millions of requests per second with low latency, such as real-time applications, gaming, and IoT.** It provides static IP addresses(1 per AZ) and supports Elastic IP(per AZ). Target groups can be IP addresses(private only), EC2 and ALB. It supports **HTTPS, HTTP & TCP** health checks.
  - **Classic Load Balancer**: Operates at both Layer 4 and Layer 7, but is considered legacy. Provides basic load balancing across multiple Amazon EC2 instances and operates at both the request level and connection level.(forbidden to use)
  - **Gateway Load Balancer**: Operates at Layer 3(Network Layer, IP protocol: GENEVE, port: 6081) and is designed to deploy, scale, and manage third-party network virtual appliances. It combines a transparent network gateway and a load balancer to distribute connections across third-party virtual appliances. It is suitable for firewalls, intrusion detection systems, and deep packet inspection systems.
  ![alt text](image-4.png)

**ALB Target Types:**

When you create a target group, you select a target type that determines how targets are registered:

| Target Type | Targets | Use case |
|---|---|---|
| **instance** | EC2 instance IDs | Standard EC2 workloads |
| **ip** | Private IP addresses (RFC 1918 ranges) | On-premises servers, containers, multi-homed instances |
| **lambda** | A single Lambda function | Serverless backends |

- **ip target type** accepts IPs from: `10.0.0.0/8`, `100.64.0.0/10`, `172.16.0.0/12`, `192.168.0.0/16`, and VPC subnets.
- This enables registering **on-premises servers** (reachable via Direct Connect or VPN) as ALB targets — traffic flows from ALB → VPC → Direct Connect/VPN → on-premises.
- **Public IPs cannot be used** as targets — only private addresses.

**ALB Weighted Target Groups:**

ALB supports assigning **weights** to multiple target groups within a single listener rule — enabling proportional traffic splitting without multiple load balancers.

- Attach multiple target groups to one rule and assign a numeric weight to each (e.g., 50 and 50 for equal split).
- **Use cases**: blue-green deployments, canary releases, hybrid migrations (split between on-premises and AWS), A/B testing.
- **NLB does NOT support Weighted Target Groups** — this feature is ALB-only.

```
ALB Listener Rule (path: /*)
    ├── Target Group A (AWS EC2 targets)     [Weight: 50]  → 50% traffic
    └── Target Group B (on-premises via DX)  [Weight: 50]  → 50% traffic
         └── ip-type targets: 10.x.x.x (on-prem servers)
```

**ALB Slow Start Mode:**

By default, when a new target is registered in a target group, ALB immediately sends it a full share of traffic. Slow Start Mode **gradually increases** the traffic sent to a new target over a ramp-up period — giving it time to warm up (initialize caches, JVM, connection pools) before receiving its full share.

- Configured per **target group** — a duration between **30 and 900 seconds** (15 minutes).
- During the ramp-up window, ALB sends a linearly increasing proportion of requests to the new target. After the window expires, the target receives its normal weighted share.
- **Disabled by default** (ramp-up duration = 0).
- Not supported with **Least Outstanding Requests** algorithm — only works with **Round Robin**.
- **Use case**: Applications with a long initialization phase — Java apps (JVM warmup), apps that pre-load large caches, or any service that degrades under full traffic before it is fully initialized.

> **Slow Start Mode vs Instance Warm-Up (ASG)**: Slow Start is an ALB feature — controls how traffic is ramped up to a target. Instance Warm-Up is an ASG feature — controls when a new instance is counted in scaling metrics. They solve different problems and can be used together.

  **Session Stickiness in Target Group**: Session stickiness (also known as session affinity) is a feature that allows the load balancer to route requests from a specific client to the same target instance for the duration of the session(expiry) using cookies. This is useful for applications that require session persistence, such as shopping carts or user sessions. Session stickiness can be configured using cookies or IP addresses.
  - Types of cookies:
    - **Application based cookies**: Cookies generated by the application itself. The load balancer uses these cookies to route requests to the same target instance.
         - **Custom Cookies**: Cookies that are generated by the application and can be configured to include specific attributes, such as expiration time and path. Can use custom cookie names. Reserved cookie names: AWSALB, AWSALBAPP, AWSALBTG.
         - **Application Cookies**: Cookies that are generated by the load Balancer and are used to maintain session state. The load balancer uses these cookies to route requests to the same target instance. Cookie name: AWSALBAPP.
    - **Duration based cookies**: Cookies that are generated by the load balancer and have a specific expiration time. The load balancer uses these cookies to route requests to the same target instance for the duration of the cookie's lifetime.Cookie name: AWSALB.
![alt text](image-5.png)

- **Cross-Zone Load Balancing**: Distributes incoming traffic across all registered targets in all enabled Availability Zones. This helps ensure that traffic is evenly distributed across all targets, regardless of their Availability Zone. Cross-Zone Load Balancing is enabled by default for Application Load Balancers(No extra charges). It is not enabled by default for NLB and Gateway Load Balancers(if enabled: extra charge applies, Classic LB is exemption).

### SSL/TLS Certificates
- SSL/TLS certificates encrypt data in transit and authenticate the server. Managed via **AWS Certificate Manager (ACM)**.
- **SNI (Server Name Indication)**: A TLS extension that allows the client to tell the server which hostname it is connecting to during the handshake — the server can then pick the correct certificate for that domain.
  - Allows **multiple certificates on a single HTTPS listener** with a single IP address — no separate IP per domain.
  - Supported by **ALB, NLB, and CloudFront** — not supported on CLB (Classic Load Balancer).
  - **Free on ALB** — bind multiple certificates to the same secure listener at no additional charge. ALB automatically selects the optimal certificate for each client.

**Multiple domain SSL strategies — comparison:**

| Approach | How | Limitation |
|---|---|---|
| **SNI on ALB** (recommended) | Upload each domain's cert to ALB, bind all to one HTTPS listener — ALB picks the right cert per request | Modern clients only (SNI requires TLS 1.0+) |
| **Wildcard certificate** (`*.example.com`) | One cert covers all subdomains | Covers subdomains only — **cannot cover multiple different domains** (e.g., `i-love-manila.com` and `i-love-boracay.com` are different domains, not subdomains) |
| **SAN (Subject Alternative Name)** | Add multiple domains to one certificate | Must **reauthenticate and reprovision the certificate** every time you add a new domain — not scalable |
| **CloudFront dedicated IP** | One dedicated IP per edge location per domain | Valid but incurs an **additional monthly charge per distribution** — not cost-effective |

> **Best practice for multiple different domains on one ALB**: Use SNI — upload a certificate per domain, bind all to the same HTTPS listener. No re-provisioning needed when adding new domains, and no extra cost.

### Cognito-Based Authentication in ALB

ALB can authenticate users **before** forwarding the request to your target — no auth code needed in your app.

```
Client request
      │
      ▼
ALB Listener Rule  (authenticate-cognito action)
      │
      ├── Not logged in? → redirect to Cognito Hosted UI (login page)
      │         │
      │         └── User logs in → Cognito issues token → redirects back to ALB
      │
      └── Logged in (valid token)? → forward request to target group
```

- **Supported on ALB only** — not NLB or GLB.
- ALB checks for a valid session cookie. If absent or expired, it redirects the user to the **Cognito Hosted UI**.
- After login, Cognito sends an authorization code back to ALB. ALB exchanges it for an ID token, access token, and refresh token.
- ALB sets an encrypted session cookie on the client — subsequent requests skip re-authentication until the session expires.
- The target (EC2, Lambda, ECS) receives the request with user identity in the `X-Amzn-Oidc-Identity` header — app knows who the user is without doing any auth itself.
- Also supports any **OIDC-compliant IdP** (Google, Okta, Active Directory) — not just Cognito.

### Connection draining/Deregistration delay
- Connection draining(for CLB) & Deregistration delay(for ALB & NLB) is a feature that allows existing connections to complete while preventing new connections from being established to instances that are being deregistered or unhealthy. The default timeout for connection draining/deregistration delay is 300 seconds (5 minutes), but it can be configured to a value between 1 and 3600 seconds (1 hour) & also can be set to 0(disabled).

### ALB Access Logs

ALB Access Logs capture **detailed information about every HTTP/HTTPS request** processed by the load balancer — stored as compressed files in an S3 bucket you specify.

- **Disabled by default** — must be explicitly enabled on the load balancer.
- **No extra charge** for enabling access logs — you only pay for S3 storage.
- Delivered to S3 approximately every **5 minutes**.

**What each log entry contains:**
- Timestamp of the request
- **Client IP address** and port
- Target IP address and port
- **Latencies**: request processing time, target processing time, response processing time
- HTTP method, request URL, protocol version
- Server response code
- Bytes sent/received
- User-Agent string
- SSL cipher and protocol (for HTTPS)

**Use cases:**
- Analyze traffic patterns (which endpoints are called most, peak hours, geographic distribution)
- Troubleshoot application errors (correlate 5xx responses with specific targets)
- Track client IP addresses for security analysis or compliance
- Feed into Athena for SQL-based log analysis directly from S3

> **ALB Access Logs vs CloudTrail for ALB**: CloudTrail only records **AWS API calls** (e.g., `CreateLoadBalancer`, `ModifyListener`) — resource/configuration changes. It does **not** capture actual HTTP request traffic flowing through the ALB. Use Access Logs for request-level traffic data; use CloudTrail for audit trail of ALB configuration changes.
