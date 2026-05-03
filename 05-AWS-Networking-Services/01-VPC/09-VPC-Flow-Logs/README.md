## VPC Flow Logs

**VPC Flow Logs** capture metadata about IP traffic going to and from network interfaces in your VPC — not the actual packet content, just the header information (who talked to whom, on what port, how much data, and whether it was accepted or rejected).

- Can be enabled at three levels: **VPC** (all ENIs), **Subnet** (all ENIs in subnet), **ENI** (specific interface).
- Logs are published to **CloudWatch Logs**, **S3** and **Kinesis Data Firehose**.
- Does **not** capture actual packet payload — only metadata.
- Does **not** capture traffic to/from `169.254.169.254` (EC2 instance metadata), DNS, DHCP, or Windows license traffic — these are excluded by default.

---

## Flow Log Record Format

Each log entry is one line representing a traffic **flow** (a connection between a source and destination):

```
version  account-id  interface-id  srcaddr    dstaddr     srcport  dstport  protocol  packets  bytes   start       end         action  log-status
2        123456789   eni-abc123    10.0.1.5   52.12.3.4   54321    443      6         10       4200    1620000000  1620000060  ACCEPT  OK
2        123456789   eni-abc123    1.2.3.4    10.0.1.5    55000    22       6         3        180     1620000010  1620000020  REJECT  OK
```

**Key fields:**
- `srcaddr / dstaddr` — source and destination IP
- `srcport / dstport` — source and destination port
- `protocol` — 6 = TCP, 17 = UDP, 1 = ICMP
- `action` — **ACCEPT** (allowed by SG/NACL) or **REJECT** (blocked)
- `log-status` — OK, NODATA (no traffic in interval), SKIPDATA (capacity exceeded)

---

## Where to Publish

| Destination | Use case |
|---|---|
| **CloudWatch Logs** | Real-time monitoring, metric filters, CloudWatch alarms (e.g. alert on rejected traffic spikes) |
| **S3** | Long-term storage, cost-effective, query with **Athena** for ad-hoc analysis |
| **Kinesis Data Firehose** | Stream to third-party SIEM tools (Splunk, Datadog, etc.) |

---

## Common Use Cases

**Security — find rejected traffic:**
```
Filter: action = REJECT
→ See which IPs are being blocked → identify port scans or misconfigured SGs/NACLs
```

**Troubleshooting — why can't EC2-A reach EC2-B:**
```
Filter: srcaddr = 10.0.1.5, dstaddr = 10.0.2.8
→ If ACCEPT: traffic reached — problem is at app layer
→ If REJECT: blocked by SG or NACL — check rules
→ If no log: traffic never left the source — check route table
```

**Compliance — audit all traffic to a sensitive resource:**
```
Filter: dstaddr = 10.0.3.100 (RDS instance)
→ Full record of which IPs accessed the database
```

---

## Flow Logs vs Traffic Mirroring

| Feature | VPC Flow Logs | VPC Traffic Mirroring |
|---|---|---|
| **What is captured** | Metadata only (IPs, ports, bytes, action) | Full packet data including payload |
| **Performance impact** | Minimal | Higher — copies actual packets |
| **Use case** | Traffic analysis, troubleshooting, compliance | Deep packet inspection, IDS/IPS, security forensics |
| **Target** | CloudWatch Logs / S3 / Firehose | EC2 instance or Network Load Balancer |
| **Cost** | Lower | Higher |
