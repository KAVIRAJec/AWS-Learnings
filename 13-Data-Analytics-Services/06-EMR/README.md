## Amazon EMR (Elastic MapReduce)

Amazon EMR is a managed big data platform for processing large-scale data using open-source frameworks.

**Key Concepts:**
- Supports **Apache Spark, Hadoop, Hive, HBase, Flink, Presto**, and more.
- Runs on EC2 instances (or **EKS** / **Serverless**) — you choose instance types and cluster size.
- **Cluster types**:
   - **Long-running**: Persistent cluster for ongoing workloads.
   - **Transient**: Spun up for a job, terminated when done — cost-efficient.
- Data stored in **S3 (EMRFS)** or **HDFS** (local to cluster nodes).
- **Node types**:
   - **Master node**: Coordinates the cluster and manages jobs.
   - **Core nodes**: Run tasks and store data in HDFS.
   - **Task nodes**: Run tasks only, no storage — can use Spot instances to reduce cost.

**Use cases:** Large-scale ETL, ML model training, log processing, genomics, financial analytics.
