## AWS Certificate Manager (ACM)

AWS Certificate Manager provisions, manages, and deploys **SSL/TLS certificates** for use with AWS services — handles the full certificate lifecycle (issuance, renewal, deployment) automatically.

- Public certificates issued by ACM are **free** — no cost for provisioning or renewal.
- ACM is **regional** — a certificate must be provisioned in the same region as the service using it. **Exception**: CloudFront requires certificates in `us-east-1` regardless of the distribution's region.

---

## Certificate Types

- **Public Certificates**: Issued by Amazon's trusted CA — trusted by all browsers and clients automatically. Free. Cannot be exported (private key never leaves ACM).
- **Private Certificates**: Issued by your own **Private CA** (AWS Private CA service) — for internal services, microservices, IoT devices. Charged per certificate issued.
- **Imported Certificates**: Bring your own certificate (from any CA) — ACM stores and deploys it, but **ACM does not auto-renew imported certs**. You manage renewal manually.

---

## Certificate Validation

Before ACM issues a certificate, it must verify you own the domain. Two methods:

- **DNS Validation** (recommended): ACM provides a CNAME record — add it to your domain's DNS (Route 53 does this automatically with one click). Once the record exists, ACM auto-renews the certificate indefinitely without any action needed.
- **Email Validation**: ACM sends an email to the domain's registered contacts — someone must click the approval link. Renewal also requires manual email approval each time.

---

## Supported Integrations

ACM certificates can only be deployed on **AWS managed load points** — not directly on EC2 instances.

- **Elastic Load Balancer (ALB / NLB)** — attach certificate for HTTPS termination.
- **CloudFront** — HTTPS for CDN distributions (must be in `us-east-1`).
- **API Gateway** — HTTPS for custom domain names.
- **Elastic Beanstalk** — HTTPS on the environment's load balancer.
- **App Runner / CloudFront / Cognito** — automatic HTTPS via ACM.

---

## Auto-Renewal

- ACM **automatically renews** public certificates before expiry (typically 60 days before).
- Renewal is fully automatic for **DNS-validated** certificates — no action needed as long as the CNAME record stays in DNS.
- **Email-validated** certificates require manual approval on every renewal.
- **Imported certificates** are never auto-renewed — ACM sends expiry notifications via EventBridge, but renewal is your responsibility.
- **AWS Config rule** `acm-certificate-expiration-check` — flags certificates expiring within a configurable number of days. Useful for catching imported certs or any cert that might slip through without renewal.

---

## ACM with Route 53

- When creating a certificate with DNS validation, ACM can **automatically insert the CNAME record** into a Route 53 hosted zone with one click — no manual DNS editing needed.
- After the record is added, validation completes within minutes.
- The CNAME record must stay in DNS permanently for auto-renewal to work — deleting it breaks future renewals.

---

## Key Limits & Gotchas

- **Cannot install ACM public certs on EC2 directly** — only on supported AWS services (ALB, CloudFront, API Gateway, etc.). For EC2, use an imported cert or terminate SSL at the load balancer.
- **CloudFront certificates must be in `us-east-1`** — even if your origin or distribution is in another region.
- **Wildcard certificates**: ACM supports `*.example.com` — covers all first-level subdomains but not the root domain or nested subdomains (`sub.sub.example.com`).
- **Private key never exposed**: For ACM-issued public certs, the private key is managed entirely by ACM — you cannot export or download it.
