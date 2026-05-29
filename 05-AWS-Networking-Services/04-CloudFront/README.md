## AWS CloudFront

Amazon CloudFront is a **Content Delivery Network (CDN)** that delivers data, videos, applications, and APIs globally with low latency by caching content at **edge locations** closer to users.

**Key Concepts:**
- **Edge Locations**: 400+ globally distributed points of presence (PoPs) that cache and serve content to nearby users, reducing round-trip to the origin.
- **Origin**: The source of the content — can be an S3 bucket, EC2 instance, ALB, or any custom HTTP server.
- **Distribution**: A CloudFront configuration that maps your origin to edge locations. Has a unique domain like `xyz.cloudfront.net`.
- **Cache Behavior**: Rules that define how CloudFront handles requests — TTL, path patterns, allowed HTTP methods, etc.
- **TTL (Time to Live)**: Controls how long content stays cached at edge locations before CloudFront re-fetches from the origin. Can be set per object using `Cache-Control` headers.
- **Invalidation**: Forcefully removes cached objects from edge locations before TTL expires. Useful after content updates (charges apply per invalidation path).
- **Origin Access Control (OAC)**: Restricts S3 bucket access so only CloudFront can read it — bucket stays private, content is served only via CloudFront.

**How it works:**
1. User requests content → routed to the nearest edge location.
2. **Cache hit** → content served directly from edge (low latency).
3. **Cache miss** → CloudFront fetches from origin, caches it at the edge, then serves the user.

**Security:**
- **DDoS Protection**: CloudFront is integrated with **AWS Shield Standard** by default — provides automatic protection against common network/transport layer (L3/L4) DDoS attacks at no extra cost.
- **AWS Shield Advanced**: Paid upgrade — adds protection against sophisticated L7 attacks, real-time attack visibility, cost protection, and access to the AWS DDoS Response Team (DRT).
- **AWS WAF (Web Application Firewall)**: Can be attached to a CloudFront distribution to filter malicious HTTP/S traffic — block SQL injection, XSS, bad bots, or specific IPs/geo locations using rules.
- **HTTPS**: Supports SSL/TLS with custom certificates via AWS Certificate Manager (ACM). Can enforce HTTPS-only or redirect HTTP → HTTPS.
- **Geo Restriction**: Block or allow users from specific countries.

**CloudFront Origins:**
- **S3 Bucket**:
   - Serve static files (images, videos, HTML) directly from S3.
   - Use **Origin Access Control (OAC)** to keep the bucket private — only CloudFront can access it.
   - OAC allows CloudFront to securely access a **private S3 bucket** — the bucket requires no public access, and direct S3 URL access is blocked.
   - CloudFront signs every request to S3 using **SigV4**. The S3 bucket policy is updated to allow only the specific CloudFront distribution.
   - Can also be used to upload files to S3 via CloudFront (PUT/POST).
- **VPC Origin**:
   - Allows CloudFront to privately connect to origins inside a VPC (e.g., ALB, EC2) without exposing them to the public internet.
   - The origin does not need a public IP — traffic flows through AWS's internal network.
   - Useful for internal applications that should only be reachable via CloudFront.
- **Custom Origin (HTTP)**:
   - Any publicly accessible HTTP/S endpoint — EC2 instance, on-premises server, or a third-party web server.
   - CloudFront forwards requests to the custom origin and caches the response.
   - Must be publicly reachable (unlike VPC Origin). Can restrict access using custom headers that only CloudFront sends.

**CloudFront Geo Restriction:**
- Allows you to control which countries can or cannot access your CloudFront distribution.
- **Allowlist**: Only users from specified countries can access the content.
- **Blocklist**: Users from specified countries are denied access.
- Country is determined using a third-party **GeoIP database** based on the user's IP.
- Use case: Copyright laws that restrict content distribution to specific regions.

**Multiple Origins & Content-Based Routing:**
- A single CloudFront distribution can route requests to **different origins based on content type** — using **cache behaviors** with path patterns.
- Example: `/static/*` → S3 bucket (static files), `/api/*` → ALB (dynamic content), `/images/*` → another S3 bucket.
- CloudFront matches path patterns in order — most specific pattern first.
- **Cannot** route based on price class — price class only controls which edge locations serve traffic, not which origin handles requests.

**Origin Groups — High Availability & Failover:**
- An **origin group** contains a **primary** and a **secondary** origin.
- If the primary returns a failure (specific HTTP status codes like 5xx, or is unavailable), CloudFront **automatically fails over** to the secondary origin.
- Use case: Primary = S3 bucket in us-east-1, Secondary = S3 bucket in eu-west-1 — automatic failover if primary bucket is unavailable.
- You assign an origin group to a cache behavior the same way you'd assign a regular origin.
- **Geo restriction is NOT for HA/failover** — it blocks/allows users by country, it has nothing to do with availability.

**Field-Level Encryption:**
- Encrypts specific **sensitive fields** (e.g., credit card numbers, SSNs) in HTTP POST requests **at the edge**, before the data reaches your origin.
- Uses **asymmetric encryption (public key)** — you provide a public key to CloudFront, only your application (with the private key) can decrypt the data.
- Encrypted at the edge, stays encrypted through your entire application stack — web servers, load balancers, and app servers never see plaintext.
- Can encrypt up to **10 fields** per request — you specify which form fields to encrypt, not the entire request body.
- **Not KMS** — CloudFront field-level encryption uses its own public/private key pair, not AWS KMS keys.

**Cache Invalidation:**
- Forces CloudFront to remove cached content from edge locations **before TTL expires**, so the next request fetches fresh content from the origin.
- Can invalidate specific paths (`/images/photo.jpg`) or all objects (`/*`).
- Charges apply per invalidation path (first 1000 paths/month are free).
- Alternatively, use **versioned file names** (e.g., `style.v2.css`) to avoid invalidation costs — CloudFront treats it as a new object.
