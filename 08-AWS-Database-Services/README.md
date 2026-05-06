## AWS Database Services

### Relational (SQL)
| Service | Description |
|---------|-------------|
| [RDS](./01-RDS/README.md) | Managed MySQL, PostgreSQL, MariaDB, Oracle, SQL Server |
| [Aurora](./03-Aurora/README.md) | MySQL/PostgreSQL-compatible, cloud-optimized, 5x faster |
| [Redshift](./04-Redshift/README.md) | Petabyte-scale data warehouse (OLAP) |

### NoSQL — Key-Value
| Service | Description |
|---------|-------------|
| [DynamoDB](./02-DynamoDB/README.md) | Serverless, single-digit ms, millions of req/sec |

### NoSQL — Document
| Service | Description |
|---------|-------------|
| [DocumentDB](./07-DocumentDB/README.md) | MongoDB-compatible managed document database |

### In-Memory
| Service | Description |
|---------|-------------|
| [ElastiCache](./05-ElastiCache/README.md) | Managed Redis & Memcached for caching |
| MemoryDB for Redis | Redis-compatible, durable, in-memory primary database (not just cache) |

### Graph
| Service | Description |
|---------|-------------|
| [Neptune](./06-Neptune/README.md) | Property graph (Gremlin) & RDF (SPARQL) |

### Time Series
| Service | Description |
|---------|-------------|
| [Timestream](./08-Timestream/README.md) | Serverless time series for IoT & operational metrics |

### Wide Column (Cassandra-compatible)
| Service | Description |
|---------|-------------|
| [Keyspaces](./09-Keyspaces/README.md) | Managed Apache Cassandra using CQL |

### Ledger
| Service | Description |
|---------|-------------|
| [QLDB](./10-QLDB/README.md) | Immutable, cryptographically verifiable transaction log |

### Search & Analytics
| Service | Description |
|---------|-------------|
| [OpenSearch](../13-Data-Analytics-Services/07-OpenSearch/README.md) | Full-text search, log analytics, real-time monitoring |
| [Athena](../13-Data-Analytics-Services/01-Athena/README.md) | Serverless SQL queries directly on S3 |
| [EMR](../13-Data-Analytics-Services/06-EMR/README.md) | Big data processing — Spark, Hadoop, Hive |

### Migration
| Service | Description |
|---------|-------------|
| [DMS (Database Migration Service)](./11-DMS/README.md) | Migrate databases to AWS with minimal downtime — supports homogeneous and heterogeneous migrations |

### Object Storage / Data Lake
| Service | Description |
|---------|-------------|
| [S3](../07-AWS-Storage-Services/01-S3/README.md) | Object storage — foundation for data lakes, queried via Athena or Redshift Spectrum |
