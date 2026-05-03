## VPC Traffic Mirroring

**VPC Traffic Mirroring** copies actual network packets from an ENI and sends them to a monitoring appliance — unlike Flow Logs which only capture metadata, Traffic Mirroring gives you the **full packet including payload**.

- Source is an **ENI** (attached to EC2) — the traffic being mirrored.
- Target is an **ENI** or a **Network Load Balancer** — where mirrored packets are sent for inspection.
- A **Mirror Filter** controls which traffic is mirrored — you can include/exclude by protocol, port, src/dst IP.
- The source and target can be in **different VPCs** (connected via peering or Transit Gateway).

---

## How It Works

```
EC2 Instance (ENI: source)
    │
    │  all traffic (or filtered subset) is duplicated
    ▼
Mirror Filter  ← defines what to capture (e.g. TCP port 80 inbound only)
    │
    ▼
Mirror Target (EC2 running IDS/IPS tool, or NLB in front of a fleet)
    │
    ▼
Security appliance analyzes the full packet (headers + payload)
```

The original traffic is **not affected** — mirroring is passive, the source EC2 continues operating normally.

---

## Key Concepts

- **Mirror Session** — ties a source ENI to a target and a filter. One ENI can have multiple sessions.
- **Mirror Filter** — inbound/outbound rules specifying which packets to capture (protocol, port range, CIDR). Unmatched traffic is not mirrored.
- **Mirror Target** — the destination for copied packets:
  - Single ENI (EC2 running Wireshark, Suricata, etc.)
  - NLB — distribute mirrored traffic across a fleet of inspection appliances.
- Traffic is encapsulated in **VXLAN (UDP port 4789)** before being sent to the target.

---

## Use Cases

- **Intrusion Detection / Prevention (IDS/IPS)** — inspect live traffic for threats, malware signatures, anomalous behaviour.
- **Security forensics** — capture full packets during or after an incident for deep investigation.
- **Performance troubleshooting** — analyze latency, retransmissions, or malformed packets that metadata alone cannot explain.
- **Compliance** — capture and archive specific traffic flows for regulatory requirements.

---

## Flow Logs vs Traffic Mirroring

| | VPC Flow Logs | VPC Traffic Mirroring |
|---|---|---|
| **Data captured** | Metadata only (IPs, ports, bytes, action) | Full packets including payload |
| **Performance impact** | Minimal | Higher — duplicates actual packets |
| **Filtering** | None — captures all traffic metadata | Yes — filter by protocol, port, CIDR |
| **Target** | CloudWatch Logs / S3 / Firehose | ENI or NLB |
| **Use case** | Traffic analysis, troubleshooting, compliance | IDS/IPS, deep packet inspection, forensics |
| **Cost** | Lower | Higher |
