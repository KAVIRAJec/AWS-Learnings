# Well-Architected Generative AI Lens

A **lens** extends the Well-Architected Framework with guidance for one kind of workload. The **Generative AI Lens** covers apps built on foundation models, on **Amazon Bedrock**, **Amazon Q**, or customer-managed models on **SageMaker AI** (classic ML workloads use the Machine Learning Lens instead). Basics of the Framework, the review Tool, and its lens/profile/template/sharing features: [Well-Architected Framework](../../03-Cloud-Architecture/01-Well-Architected-Framework/README.md).

**Remember:** it keeps the six pillars and adds GenAI-specific questions across the **lifecycle**: *scoping → model selection → customization → development → deployment → continuous improvement*, with **responsible AI** throughout (producers, providers, and consumers of a model share responsibility).

## What each pillar focuses on

| Pillar | GenAI focus |
|---|---|
| **Operational excellence** | Consistent output quality, monitoring, traceability, automated lifecycle management, deciding **when** to customize a model |
| **Security** | Protect model endpoints, mitigate harmful outputs and **excessive agency**, secure prompts, defend against **model poisoning**, audit events |
| **Reliability** | Throughput limits, reliable component communication, observability, graceful failure, **versioned artifacts**, distributed inference |
| **Performance efficiency** | Measure and improve model performance, right-size compute, improve **data retrieval** performance |
| **Cost optimization** | Pick cost-appropriate models, balance cost vs quality of inference, **prompt engineering for cost**, optimize vector stores and agent workflows |
| **Sustainability** | Use less compute for training, customization, hosting, data processing, storage; model-efficiency techniques and serverless |

## Using it

- It is **not built into** the Well-Architected Tool as a catalog item in the docs I read: you **download the lens JSON** from the public AWS custom-lens repository and **import it as a custom lens** into the Tool, then review your GenAI workload against it.
- Run the review at the start (scoping/model selection) and again after major changes; track risks with milestones.
- The pillar areas map to notes here: security → [Guardrails](../02-Amazon-Bedrock/04-Guardrails/README.md) and [federated access](../09-Security-Governance-Responsible-AI/02-Federated-Access-to-Foundation-Models.md); cost and performance → [hosting choice](Choosing-How-to-Host-an-FM.md) and [Prompt Caching](../02-Amazon-Bedrock/06-Inference-Throughput-and-Routing/01-Prompt-Caching.md); customization decision → [Choosing a Customization Approach](Choosing-a-Customization-Approach.md).
