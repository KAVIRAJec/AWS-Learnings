## AWS Application Discovery Service

**AWS Application Discovery Service** helps you plan a migration to AWS by automatically collecting configuration and performance data from your on-premises data center — so you know exactly what you have before you migrate.

- Data is stored and viewable in **AWS Migration Hub** (central migration tracking dashboard).
- Helps answer: "What servers do I have? What runs on them? How much CPU/memory do they use? What talks to what?"
- Collected data is used to right-size EC2 instances and identify application dependencies.

---

## Discovery Modes

### Agentless Discovery (Discovery Connector)
- A virtual appliance (OVA) deployed in **VMware vCenter**.
- No agent installed on individual VMs — discovery happens at the hypervisor level.
- Collects: VM inventory, resource allocation (CPU, RAM, disk), utilization metrics.
- **Does not** collect process-level data or network connection details.
- Best for: quick inventory of VMware environments.

```
VMware vCenter
    │
    │  Discovery Connector (OVA appliance)
    ▼
Collects VM config + utilization
    │
    ▼
AWS Migration Hub (view inventory + metrics)
```

### Agent-Based Discovery (Discovery Agent)
- A lightweight agent installed on **each server** (Windows or Linux).
- Collects deeper data: running processes, network connections (what talks to what), detailed performance metrics.
- Works for **any server** — physical, VMware, Hyper-V, or other hypervisors.
- Best for: mapping application dependencies before migration.

```
Server A (Discovery Agent installed)
    │  processes, network connections, CPU/RAM/disk metrics
    ▼
AWS Migration Hub
    │
    ▼
Dependency map: Server A ──► Server B (port 3306) ──► RDS-like pattern detected
```

---

## What It Collects

| Data | Agentless | Agent-Based |
|---|---|---|
| Server hostname, OS, IP | ✓ | ✓ |
| CPU, RAM, disk allocation | ✓ | ✓ |
| Average/peak utilization | ✓ | ✓ |
| Running processes | ✗ | ✓ |
| Network connections (ports, peers) | ✗ | ✓ |
| Application dependency mapping | ✗ | ✓ |

---

## Integration with AWS Migration Hub

All discovery data flows into **AWS Migration Hub** — a single place to:
- View your complete server inventory.
- Group servers into **applications** (e.g., "web tier + app tier + DB" = one application).
- Track migration status per application across DMS, MGN, and other migration tools.
- Export data to S3 for analysis with Athena.

---

## Typical Migration Planning Flow

```
Step 1: Deploy Discovery Connector (agentless) → get full inventory
Step 2: Install Discovery Agent on key servers → map dependencies
Step 3: Group servers into applications in Migration Hub
Step 4: Analyze utilization → right-size EC2 recommendations
Step 5: Prioritize migration waves (which apps to migrate first)
Step 6: Execute migration using AWS MGN or DMS
```
