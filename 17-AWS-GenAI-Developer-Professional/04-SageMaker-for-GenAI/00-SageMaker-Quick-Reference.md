# Amazon SageMaker AI: One-Page Reference

SageMaker AI is AWS's managed platform to **prepare data, build, train, evaluate, deploy, and govern** ML and foundation models. This page lists every major piece by lifecycle stage so you can find the right one fast. Deeper SAA-level notes: [SageMaker](../../14-Machine-Learning-Services/01-SageMaker/README.md).

> **Names:** *Amazon SageMaker AI* (renamed Dec 2024; API/CLI names still say `sagemaker`) is the ML service. *Amazon SageMaker* is now also the umbrella for the unified data + analytics + AI platform (Unified Studio, Lakehouse, Catalog) that includes SageMaker AI and Bedrock.

## 1. Prepare data

| Tool | What it does |
|---|---|
| **Processing jobs** | Run your script/container once on managed instances for preprocessing, feature engineering, validation, evaluation (details below) |
| **Data Wrangler** | Visual data preparation and feature engineering |
| **Ground Truth** | Data labeling with human workers and automated labeling |
| **Feature Store** | Shared feature repository: **online** (low-latency) and **offline** (S3) |

## 2. Build and experiment

| Tool | What it does |
|---|---|
| **Studio** (and JupyterLab) | Web IDE for the whole workflow; Studio Classic is the legacy version |
| **Notebook instances** | Managed Jupyter servers |
| **JumpStart** | Catalog of pretrained and foundation models to deploy or fine-tune |
| **Autopilot** | AutoML: tries algorithms and hyperparameters, shows its work |
| **Canvas** | No-code model building for business users |
| **Experiments** | Track and compare runs |

## 3. Train

| Option | Use it for |
|---|---|
| **Training jobs** | Managed training on chosen instances; built-in algorithm, framework, or your container; **managed Spot** (up to 90% cheaper, checkpoint to S3); **distributed** data/model parallelism |
| **Automatic Model Tuning** | Hyperparameter search over many training jobs |
| **HyperPod** | **Resilient clusters** for large FM training: auto-detects and replaces faulty nodes and resumes from checkpoints; orchestrated by **Slurm** or **EKS**; training plans and UltraServers for large GPU fleets |
| **Debugger** | Spot training problems (such as overfitting) and stop a failing job |
| **JumpStart fine-tuning** | Instruction fine-tuning or domain adaptation, with LoRA options ([LoRA lifecycle](01-LoRA-Adapter-Lifecycle.md)) |

## 4. Evaluate, monitor, govern

| Tool | What it does |
|---|---|
| **Clarify** | Bias detection, explainability (SHAP), and foundation-model evaluation jobs (also the `fmeval` library for models outside AWS) |
| **Model Monitor** | Watches endpoints for data drift, quality drop, bias drift |
| **Model Registry** | Versions and approval status (`PendingManualApproval`, `Approved`, `Rejected`) that gate deployment |
| **Model Cards** | Documentation and risk record per model ([model cards in the pipeline](02-Model-Cards-in-the-Pipeline.md)) |
| **Pipelines** | ML CI/CD as a DAG: Processing, Training, Condition, Register steps |

## 5. Deploy: inference variants

| Variant | Best for | Key facts |
|---|---|---|
| **Real-time endpoint** | Interactive, low latency | Always-on instances, pay while running |
| **Serverless Inference** | Idle gaps, tolerates cold starts | Pay per request, scales to zero; **no GPU**; 1–6 GB memory; 200 concurrent invocations per endpoint |
| **Asynchronous Inference** | Big payloads, long jobs | Queue; up to 1 GB payload, up to 1 hour; results to S3; can scale to zero |
| **Batch Transform** | Offline scoring of a dataset in S3 | No persistent endpoint |
| **Multi-model endpoint** | Many small models, sparse traffic | One endpoint hosts many models |
| **Inference components** | Hosting FMs on shared compute; **LoRA adapters**; **scale to zero** | At zero instances calls fail for minutes until one provisions |
| **Neo** | Cheaper/faster inference | Compiles a model for target hardware (such as Inferentia) |
| **Auto scaling** | Match instances to traffic | Application Auto Scaling on endpoints |

How to deploy: **JumpStart in Studio** (no code), **ModelBuilder** in the Python SDK (control), **Boto3/CloudFormation** (repeatable, at scale). Which hosting option to pick: [Choosing How to Host an FM](../01-GenAI-Fundamentals/Choosing-How-to-Host-an-FM.md).

---

## SageMaker Processing jobs in short

**What it is:** a **one-off managed job**. You submit a script or container; SageMaker starts the instances, copies the input from S3, runs it, writes outputs to S3, and **shuts the instances down**. You pay only for the run. It is separate from training and from endpoints.

- **Inputs:** data in **S3** (Athena or Redshift can also be sources).
- **Containers:** built-in processing containers (scikit-learn, Spark for distributed work) or **your own container** with custom libraries.
- **Created with:** `CreateProcessingJob` (SDK/CLI) or as a **Processing step** in SageMaker Pipelines.
- **Monitoring:** CloudWatch logs and CPU/GPU/memory/disk metrics.
- **GenAI uses:** clean and normalize text, batch-validate data, multimodal preprocessing, and large-scale prep before embedding or fine-tuning; also runs model **evaluation** and Clarify analyses.

**Processing vs the alternatives**

| Need | Pick |
|---|---|
| Heavy or custom Python/Spark prep on big S3 data, part of an ML pipeline | **SageMaker Processing** |
| General ETL and data quality across analytics sources | **AWS Glue** (Glue Data Quality) |
| Small, quick, event-driven transforms | **Lambda** |
| Visual, interactive prep | **Data Wrangler** |
