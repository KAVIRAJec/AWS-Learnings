## Amazon ElastiCache

Amazon ElastiCache is a fully managed **in-memory caching service** supporting **Redis** and **Memcached** — reduces DB load and improves application performance with sub-millisecond latency.

**When to use ElastiCache:**
- **Read-heavy workloads**: Store frequently read objects in cache instead of hitting the DB every time — social networking, gaming, media sharing, leaderboards, Q&A portals.
- **Compute-intensive workloads**: Cache the result of expensive computations — recommendation engines, ML inference results.
- Any workload where the same data is read repeatedly and low latency matters.

**Redis:**
- Supports clustering, Multi-AZ with auto-failover and read replicas for high availability.
- Data persistence (RDB snapshots + AOF logs) — survives restarts.
- Same as RDS - Backup, Snapshots, Point-in-time recovery.
- Supports **IAM Auth** (AWS-level) and **Redis AUTH** (password at Redis level).
- Supports sorted sets, pub/sub, Lua scripting.
- Use cases: Leaderboards, real-time ranking, session store, pub/sub.

**Memcached:**
- Simple key-value store — no persistence, no replication, no failover.
- Multi-node for data partitioning (sharding). Multi-threaded architecture.
- Use cases: Simple caching where durability is not needed.

**Caching Patterns:**
- **Lazy Loading**: Load data into cache only on cache miss. Cache may serve stale data.
- **Write-Through**: Write to cache and DB simultaneously — cache always fresh, but more write overhead.
- **Session Store**: Store user session data in ElastiCache with TTL — keeps application stateless.

**Note**: Requires application-level code changes to implement caching logic.
