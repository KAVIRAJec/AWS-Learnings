## Amazon DynamoDB

Amazon DynamoDB is a fully managed **serverless NoSQL** database — key-value and document model — with single-digit millisecond performance at any scale. Handles **millions of requests per second**, stores **trillions of rows**, and scales to **100s of TBs of storage** automatically.

**Key Concepts:**
- **Table**: Collection of items (rows). No fixed schema — each item can have different attributes.
- **Primary Key**: Either a **Partition Key** alone, or a **Partition Key + Sort Key(optional)** (composite key).
- **Attribute**: A key-value pair within an item (equivalent to a column).
- **High availability by default**: Data replicated across 3 AZs automatically.
- **Maximum item size**: 400 KB (including attribute names and values).

**Capacity Modes:**
- **Provisioned**: You specify read/write capacity units (RCU/WCU). Supports Auto Scaling. Cost predictable.
- **On-Demand**: Automatically scales to any traffic level. Pay per request. No capacity planning — best for unpredictable workloads.

**Read Consistency:**
- **Eventually Consistent Reads**: Default — may return slightly stale data (replicated asynchronously).
- **Strongly Consistent Reads**: Always returns the most up-to-date data. Costs 2x RCU.

**DynamoDB Accelerator (DAX):**
- In-memory cache for DynamoDB — microsecond read latency.
- Fully managed, Multi-AZ. No application code changes needed.
- Default TTL of 5 minutes for cached items(Can be changed).

| | DAX | ElastiCache |
|-|-----|-------------|
| Works with | DynamoDB only | Any database (RDS, DynamoDB, etc.) |
| Latency | Microseconds | Sub-millisecond |
| Code changes | None (drop-in) | Requires app-level caching logic |
| Use case | Individual object cache, query cache | Aggregation results, complex computed data |

**DynamoDB Streams:**
- Ordered stream of item-level changes (insert, update, delete) in a table.
- Retained for 24 hours. Limited consumer options. Consumed by Lambda triggers or DynamoDB Stream Kinesis adapter.

**Kinesis Data Streams**: 
- 1 year retention, multiple consumers, more control over processing. Ideal for real-time analytics, data replication, complex event processing. 
- Many consumers can read the same stream data independently.

**Global Tables:**
- Multi-region, multi-active(multi-way) replication — read and write from any region.
- Low-latency global access, built-in conflict resolution (last writer wins).
- Requires DynamoDB Streams enabled.

**TTL (Time to Live):**
- Automatically delete items after a specified expiry timestamp — no extra cost.

**Backups:**
- **PITR (Point-in-Time Recovery)**: 
    - Continuous backups up to 35 days. 
    - Can recover any point in time within that window. 
    - Backups are stored in S3
- **On-Demand Backup**: Full backups retained until manually deleted. Does not impact table performance.

**Transactions:**
- Supports ACID transactions across multiple items/tables via `TransactWriteItems` and `TransactGetItems`.

**Table Classes:**
- **DynamoDB Standard**: Default class — optimized for frequently accessed data. Higher storage cost, lower read/write cost.
- **DynamoDB Standard-IA (Infrequent Access)**: For tables where data is rarely accessed. Lower storage cost (~60% cheaper), slightly higher read/write cost. Ideal for logs, old records, audit history where data is stored long-term but queried infrequently.
- Table class can be changed at any time with no downtime.
