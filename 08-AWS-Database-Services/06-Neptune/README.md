## Amazon Neptune

Amazon Neptune is a fully managed **graph database** — optimized for storing, querying, and traversing highly connected datasets where relationships between data are as important as the data itself.

**Key Concepts:**
- **Graph Model**: Data stored as nodes (entities) and edges (relationships) with properties on both.
- **Supported Query Languages**:
   - **Gremlin** — for Property Graph model (Apache TinkerPop).
   - **SPARQL** — for RDF (Resource Description Framework) graph model.
   - **openCypher** — Cypher query language for Property Graph (similar to Neo4j).
- **Storage**: Auto-scales up to 64 TB. 6 copies across 3 AZs — highly durable.
- **Availability**: Up to 15 read replicas, automatic failover in < 30 seconds.
- **Backup**: Continuous backup to S3, point-in-time recovery up to 35 days.
- **Neptune Streams**: Ordered stream of every change to the graph — consumed by Lambda or other services for real-time sync.

**Provisioning:**

- **Provisioned (DB Cluster)**:
   - You choose the instance type for the primary writer and read replicas.
   - Cluster consists of one **primary instance** (reads + writes) and up to 15 **read replicas** (reads only).
   - Shared storage volume across all instances — storage auto-scales, no need to pre-provision.
   - Automatic failover to a read replica if the primary fails (< 30 seconds).
   - Best for: Steady, predictable graph workloads needing consistent performance.

- **Neptune Serverless**:
   - Automatically scales Neptune Capacity Units (NCUs) up and down based on actual workload — no instance selection needed.
   - You set a min and max NCU range — Neptune stays within that range.
   - Automatically pauses when idle (if configured) to save cost.
   - Best for: Variable or unpredictable workloads, dev/test environments, infrequent graph queries.

| | Provisioned | Serverless |
|-|------------|------------|
| Capacity | Fixed instance type | Auto-scales (NCUs) |
| Cost model | Pay per instance hour | Pay per NCU-hour used |
| Best for | Steady workloads | Variable / infrequent workloads |

**Pricing:**
- **Provisioned**: Pay per instance hour (primary + replicas) + storage per GB-month + I/O per million requests + backup storage.
- **Serverless**: Pay per NCU-hour consumed + storage per GB-month + I/O per million requests.
- Data transfer within the same region (between Neptune and EC2) is free. Cross-region transfer is charged.

**Use cases:** Social networks, knowledge graphs, fraud detection, recommendation engines, identity graphs, network/IT topology.
