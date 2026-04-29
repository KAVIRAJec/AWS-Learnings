## AWS Deep Learning AMIs (DLAMIs)

AWS Deep Learning AMIs are **pre-configured Amazon Machine Images** for EC2 that come with popular deep learning frameworks, GPU drivers, and libraries pre-installed — eliminating the manual setup required to start deep learning work on EC2.

**Key Concepts:**
- Launch an EC2 instance (typically a GPU instance — P3, P4, G4, G5) with the DLAMI and get a ready-to-use environment in minutes instead of hours.
- Available for **Ubuntu** and **Amazon Linux 2**.

**DLAMI Types:**

| Type | Description |
|------|-------------|
| **Deep Learning AMI (CUDA)** | All major frameworks pre-installed — choose the one you want at launch time |
| **Deep Learning AMI (GPU PyTorch/TensorFlow)** | Optimized single-framework AMIs — leaner, faster startup |
| **Deep Learning Base AMI** | Only CUDA, cuDNN, and Nvidia drivers — bring your own framework |

**Pre-installed Frameworks:**
- PyTorch, TensorFlow, Apache MXNet, JAX, Hugging Face Transformers
- CUDA, cuDNN, NCCL (GPU communication for distributed training)
- Jupyter, conda environments per framework (isolated, no dependency conflicts)

**When to Use DLAMIs vs SageMaker:**

| | Deep Learning AMIs | SageMaker |
|---|---|---|
| Control | Full EC2 access — ssh, configure anything | Managed, less direct access |
| Setup | AMI pre-configures environment | Fully managed containers |
| Cost | Pay for EC2 directly | SageMaker overhead |
| Use case | Research, custom setups, long-running experiments | Production training, MLOps pipelines |

**Use cases:** Deep learning research and experimentation, running custom training scripts directly on GPU instances, framework evaluation, development environments for ML engineers who need full system access.
