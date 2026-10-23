# Bedrock Provisioned Throughput

By default you call Bedrock models on demand and share capacity with everyone else, paying per token. **Provisioned Throughput** instead reserves a fixed amount of capacity for one model at a fixed hourly price, so you get predictable throughput under heavy load. It is also **required to run a model you customized in Bedrock**.

**Key ideas**
- **Model unit (MU)**: the unit of capacity you buy. One MU delivers a set number of input tokens and output tokens it can process per minute for that model. How many MUs you need depends on your traffic; the per-MU figures and limit increases come through your AWS account manager.
- **Hourly billing**: you pay by the hour for the capacity whether or not you use it, **until you delete it**.
- **Price**: depends on the model, the number of MUs, and the commitment length. A custom model costs the same per hour as the base model it was customized from.
- **Using it**: once created, you call the **provisioned model** (its ARN) in place of the model ID.

**Commitment length** trades flexibility for a lower hourly price:
- **No commitment**: delete any time, highest hourly price.
- **1 month**: can't delete until the term ends.
- **6 months**: can't delete until the term ends, lowest hourly price.

**When it is the right choice**
- A customized model that must be served (Bedrock requires it).
- Steady, high, latency-sensitive traffic where on-demand throttling or variability is a risk.
- Capacity planning for token throughput, rather than relying on shared quotas.

> [!WARNING]
> Capacity you don't use is still billed, and a committed term can't be cancelled early. Size MUs from measured token throughput, and delete test provisioned models you no longer need.

Imported models (Custom Model Import) are the exception: they can use on-demand throughput. See [Bedrock Model Customization](../07-Model-Customization/01-Bedrock-Model-Customization.md).
