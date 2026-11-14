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

| Variant | Best for | You pay for | GPU | Scale to zero | Request limits | Key facts |
|---|---|---|---|---|---|---|
| **Real-time endpoint** | Interactive, low latency, steady traffic | Instance-hours, always on | **Yes** | **No** by default; **yes** only when hosting inference components | Synchronous | Widest features: multiple production variants (A/B, shadow), data capture, Model Monitor, VPC, inference pipelines |
| **Serverless Inference** | Idle gaps, spiky traffic, tolerates cold starts | Per request duration (+ Provisioned Concurrency if used) | **No** | **Yes**, automatic (cold start on next call) | Memory 1–6 GB; **200 concurrent** per endpoint; 50 endpoints per Region; image up to 10 GB | **Provisioned Concurrency** keeps workers warm; no multi-model, data capture, Model Monitor, VPC |
| **Asynchronous Inference** | Big payloads, long jobs, latency-tolerant | Instance-hours while running | **Yes** | **Yes**, requests wait in the queue | Payload up to **1 GB**; processing up to **1 hour** | Payload in S3 → `InvokeEndpointAsync` → result in S3; optional SNS notifications; async-only endpoint |
| **Batch Transform** | Offline scoring of a dataset in S3 | Instance-hours for the job only | Yes | **n/a**: no endpoint, instances stop after the job | Whole dataset in S3 | Set `SplitType` to match input format |
| **Multi-model endpoint** | Many small similar models, sparse traffic | Shared instance-hours | Yes | No | Models **loaded on demand** from S3 | One container serves many models |
| **Multi-container endpoint** | Different frameworks sharing instances | Shared instance-hours | Yes | No | Call a container directly or chain them as a pipeline | Distinct from multi-model: different containers |
| **Inference components** | FMs on shared compute; **LoRA adapters**; per-model scaling | Instance-hours while copies run | **Yes**: each component reserves accelerators and memory | **Yes**: `MinInstanceCount = 0` + step scaling on `NoCapacityInvocationFailures` | At zero, calls **fail for several minutes** until an instance provisions | Needs a real-time endpoint; see [LoRA](01-LoRA-Adapter-Lifecycle.md) |
| **Neo** | Cheaper or faster inference | n/a (an optimization step) | Targets GPUs, Inferentia, CPU | n/a | n/a | Compiles a model ahead of time for the target hardware |
| **Inference Recommender** | Choosing instance type, count and config by measurement | Only the instances used while the job runs | Tests whichever instance types you pick | n/a | Up to **10** instance types or a serverless config per job | Load-tests your model and returns cost and latency per option; see [below](#inference-recommender-in-short) |
| **Auto scaling** | Match instances to traffic | n/a | n/a | Only as above | n/a | Application Auto Scaling: target tracking, step, scheduled; cooldowns; scale-in is slower than scale-out |

LLM-specific speedups (fast model loading, quantization, compilation, speculative decoding): [LLM inference optimization](04-LLM-Inference-Optimization.md).

How to deploy: **JumpStart in Studio** (no code), **ModelBuilder** in the Python SDK (control), **Boto3/CloudFormation** (repeatable, at scale). Which hosting option to pick: [Choosing How to Host an FM](../01-GenAI-Fundamentals/Choosing-How-to-Host-an-FM.md).

## Inference Recommender in short

**What it is:** automated load testing that answers "which instance type, and how many, should host this model?". It deploys your model to temporary endpoints on candidate instances (or a serverless config), sends it traffic, and reports cost and performance for each, so you pick by measurement instead of guessing. You pay only for the instances while the job runs.

**Inputs:** a SageMaker model or a model version registered in **Model Registry**, an IAM role, and a **sample payload** (the load test replays it). Run it from the console, SDK, or CLI (`CreateInferenceRecommendationsJob`).

| | **Instance recommendations** (default job) | **Custom load test** (`Advanced` job) |
|---|---|---|
| You provide | Model + sample payload | Also a **traffic pattern**, **stopping conditions**, and the instance types |
| Does | Runs a set of load tests on recommended instance types or a serverless endpoint | Benchmarks to **your** latency and throughput requirements |
| Use for | A quick shortlist | Validating against a production target |

**Custom load test settings**
- **Traffic pattern**: *phases* (users spawned per minute; 1–3 initial users, spawn rate 0–3, each phase 120–3,600 s) or *stairs* (add users in timed steps). A "user" is a system-generated client looping requests.
- **Stopping conditions** (job stops when any is met): `MaxInvocations` (requests per minute, up to **30,000**), `ModelLatencyThresholds` (a percentile such as P95 and a value in ms), and `FlatInvocations` (`Stop` by default when throughput stops rising, meaning the endpoint is at capacity).
- **Limits**: up to **10** tests and 10 in parallel per job; a job can take **up to 2 hours**.

**Results per option:** `CostPerHour`, `CostPerInference`, max invocations per minute, `ModelLatency` (in microseconds; includes round trip to the container), and a recommended `InitialInstanceCount` (how many instances to meet your `MaxInvocations`). Serverless results add CPU and memory use and `ModelSetupTime` (cold start). Console views let you weight cost, latency and throughput.

**Where it fits:** instance choice for [real-time or serverless endpoints](#5-deploy-inference-variants); it can also test models **compiled with Neo** and tune environment parameters. For LLM-specific speedups see [LLM inference optimization](04-LLM-Inference-Optimization.md).


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
