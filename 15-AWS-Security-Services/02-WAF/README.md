## AWS WAF (Web Application Firewall)

AWS WAF is a **Layer 7 (HTTP/HTTPS) firewall** that protects web applications from common exploits, bots, and malicious traffic — by inspecting HTTP requests and allowing, blocking, or counting them based on rules you define.

- Operates at **Layer 7** — inspects HTTP method, headers, body, URI, query strings, and IP.
- Deployed on: **CloudFront, ALB, API Gateway, AppSync, Cognito User Pools, App Runner**.
- Does **not** protect NLB or EC2 directly — only on supported managed entry points.

---

## Core Concepts

- **Web ACL (Access Control List)**: The top-level resource — contains all rules and rule groups. Attached to a CloudFront distribution, ALB, or API Gateway. Defines the default action (Allow / Block) for requests that match no rules.
- **Rule**: A condition that inspects a request and takes an action — `Allow`, `Block`, or `Count`.
- **Rule Group**: A reusable collection of rules — can be AWS Managed, third-party (Marketplace), or your own.
- **Match Statement**: What the rule inspects — IP address, geo location, string match, regex, size, SQL injection pattern, XSS pattern, rate limit, etc.
- **Action**:
  - **Allow** — forward the request to the origin.
  - **Block** — return HTTP 403 to the client.
  - **Count** — let the request through but increment a counter (useful for testing rules before enforcing).
  - **CAPTCHA / Challenge** — present a CAPTCHA or browser challenge to verify it's a human.
- **WCU (Web ACL Capacity Units)**: Each rule consumes WCUs based on complexity. Default limit: **1,500 WCU per Web ACL**.

---

## Rule Types

**AWS Managed Rule Groups** — pre-built by AWS, updated automatically:
- `AWSManagedRulesCommonRuleSet` — protects against OWASP Top 10 (SQLi, XSS, etc.)
- `AWSManagedRulesKnownBadInputsRuleSet` — blocks known malicious input patterns
- `AWSManagedRulesAmazonIpReputationList` — blocks IPs with bad reputation (bots, scrapers)
- `AWSManagedRulesBotControlRuleSet` — detects and controls bot traffic

**Rate-Based Rules** — limit requests from a single IP over a time window:
- e.g., block any IP sending more than 2,000 requests in 5 minutes.
- Useful for brute-force and DDoS mitigation at Layer 7.

**Custom Rules** — write your own match conditions:
- Match on specific URI path, header value, query string, body content, country, IP set.
- Combine conditions with AND / OR / NOT logic.

---

## IP Sets & Geo Match

- **IP Set**: A reusable list of IP addresses (IPv4/IPv6 CIDRs) — reference in rules to allow or block specific IPs.
- **Geo Match**: Allow or block requests based on the **country of origin** — uses the IP to determine country.
  - Use case: restrict an internal app to requests only from your country, or block traffic from high-risk regions.

---

## WAF Logging

- Enable logging to **S3, CloudWatch Logs, or Kinesis Firehose** — logs every request with the rule that matched and the action taken.
- Use logs to audit traffic, tune rules, and investigate attacks.
- **Sampled requests**: WAF shows a sample of recent requests in the console for each rule — useful for testing without full logging enabled.

---

## WAF vs Shield vs Firewall Manager

| | WAF | Shield | Firewall Manager |
|---|---|---|---|
| **Protects against** | Layer 7 exploits (SQLi, XSS, bots) | DDoS (Layer 3/4 and Layer 7) | Centrally manages WAF + Shield across accounts |
| **Layer** | 7 (HTTP/HTTPS) | 3/4 (Standard), 3/4/7 (Advanced) | Management plane |
| **Setup** | Per Web ACL, per resource | Automatic (Standard), opt-in (Advanced) | Org-wide policy enforcement |
| **Cost** | Per Web ACL + per rule + per request | Standard: free, Advanced: $3,000/month | Per policy per region per account |
| **Best for** | App-layer protection, OWASP rules | DDoS resilience | Multi-account WAF/Shield governance |
