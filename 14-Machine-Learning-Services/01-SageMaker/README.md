## Amazon SageMaker

Amazon SageMaker is a fully managed **end-to-end ML platform** — covers the complete machine learning lifecycle: data labeling, feature engineering, training, hyperparameter tuning, deployment, and monitoring — all in one place.

**Key Concepts:**

- **SageMaker Studio**: Web-based IDE for the full ML workflow — notebooks, experiments, pipelines, and model registry in a unified UI.
- **SageMaker Notebook Instances**: Managed Jupyter notebooks with pre-installed ML frameworks (TensorFlow, PyTorch, MXNet, Scikit-learn).
- **SageMaker Ground Truth**: Data labeling — use human labelers or automated labeling (active learning) to build training datasets. Reduces labeling cost by up to 70% using active learning.
- **SageMaker Feature Store**: Centralized repository for storing and sharing ML features. **Online store** (low-latency, real-time) and **Offline store** (S3-based, for training). Prevents feature duplication across teams.
- **SageMaker Experiments**: Track, compare, and manage ML runs — captures inputs, parameters, metrics, and outputs automatically.

**Training:**
- **Training Jobs**: Managed training on your choice of EC2 instance — single or distributed across multiple instances.
- **Managed Spot Training**: Use EC2 Spot Instances — save up to **90%** on training cost. Checkpoints to S3 allow resuming interrupted jobs.
- **Automatic Model Tuning (HPO)**: Runs multiple training jobs with varying hyperparameters — finds the optimal values using Bayesian or random search.
- **Distributed Training**: Data parallelism and model parallelism for large models that don't fit on a single GPU.
- **SageMaker Debugger**: Detects training issues (vanishing gradients, overfitting) in real time — can auto-stop a failing training job.
- **SageMaker Processing**: Run preprocessing, postprocessing, and model evaluation scripts on managed infrastructure — uses Spark or custom containers.

**AutoML & No-Code:**
- **SageMaker Autopilot**: AutoML — automatically explores algorithms and hyperparameters to find the best model. Full transparency — generates notebooks showing every step.
- **SageMaker Canvas**: No-code ML for business users — point-and-click model building and predictions without writing code.
- **SageMaker JumpStart**: Pre-trained foundation models and solution templates — deploy models like Llama, Stable Diffusion with one click.

**Deployment Options:**

| Mode | Description |
|------|-------------|
| **Real-time Endpoint** | Always-on HTTPS endpoint — low-latency synchronous inference |
| **Serverless Inference** | Auto-scales from zero — pay per request, no idle cost |
| **Asynchronous Inference** | Queue large/long-running requests — results written to S3 |
| **Batch Transform** | One-off inference on a dataset in S3 — no persistent endpoint needed |
| **Multi-Model Endpoint** | Host many models on one endpoint — cost-efficient for sparse traffic |

**MLOps:**
- **SageMaker Pipelines**: ML CI/CD — build, automate, and version end-to-end workflows as DAGs (directed acyclic graphs).
- **SageMaker Model Registry**: Track, version, and approve models before deployment — enforces governance over model promotion to production.
- **SageMaker Clarify**: Detect bias in data and models; generate feature importance explanations (SHAP values).
- **SageMaker Model Monitor**: Continuously monitor deployed endpoints for data drift, model quality degradation, and bias drift — triggers CloudWatch alarms.

**Integrations:** S3 (data/artifacts), ECR (custom containers), VPC (private training/inference), CloudWatch (metrics/logs), Step Functions (orchestration), EventBridge (pipeline triggers).

**Use cases:** Custom model training and deployment, AutoML, feature management, ML pipelines, model governance, data labeling, foundation model fine-tuning.
