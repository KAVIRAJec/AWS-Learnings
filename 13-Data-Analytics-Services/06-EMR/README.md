## Amazon EMR (Elastic MapReduce)

Amazon EMR is a managed **big data platform** for processing and analyzing large-scale data using popular open-source frameworks — without managing the underlying infrastructure.

**Supported Frameworks:** Apache Spark, Hadoop, Hive, HBase, Flink, Presto, Pig, and more.

**Key Concepts:**

- **Node Types**:
   - **Primary Node (Master)**: Coordinates the cluster — manages resource allocation, job scheduling, and HDFS NameNode.
   - **Core Nodes**: Run tasks and store data in **HDFS** — must have at least one. Terminating a core node risks data loss.
   - **Task Nodes**: Run tasks only, no HDFS storage — optional, can be Spot instances to save cost. Safe to add/remove anytime.

- **Storage Options**:
   - **EMRFS (S3)**: Store data in S3 — persistent beyond cluster lifetime. Recommended for most workloads.
   - **HDFS**: Local distributed storage on cluster nodes — faster but lost when cluster terminates.
   - **EBS**: Attached block storage per node — temporary, lost on termination.

- **Cluster Lifecycle**:
   - **Long-running**: Persistent cluster — keep alive for interactive workloads, ongoing processing.
   - **Transient**: Spun up for a specific job, auto-terminates when done — cost-efficient for batch jobs.

**Deployment Options:**
- **EMR on EC2**: Classic — full control over instance types, storage, and configuration.
- **EMR on EKS**: Run EMR workloads on an existing EKS cluster — share Kubernetes infrastructure.
- **EMR Serverless**: No cluster management — auto-provisions workers per job, scales to zero when idle.

**Cost Optimization:**
- Use **Spot Instances** for task nodes — significantly reduces cost for fault-tolerant workloads.
- Use **Reserved Instances** for primary and core nodes (always on).
- Store data in **S3** and use transient clusters — pay only for compute during processing.

**Use cases:** Large-scale ETL, ML preprocessing, log processing, genomics, financial risk modeling, data lake transformation.
