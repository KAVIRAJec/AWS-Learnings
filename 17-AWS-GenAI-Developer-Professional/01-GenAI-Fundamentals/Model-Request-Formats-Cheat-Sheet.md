# Model Request Formats: Quick Reference

Same task, different shapes: the request body depends on **which API** you call and **which model family** is behind it. When a question gives you the API plus a few field names, match them here.

**Remember:** `Converse` (Bedrock) is the one uniform shape. Everything else (Bedrock `InvokeModel`, SageMaker `invoke_endpoint`) is **model-specific**.

## Which API uses which shape

| API | Request shape | Content type |
|---|---|---|
| **Bedrock `Converse` / `ConverseStream`** | `messages` + `inferenceConfig` (`maxTokens`, `temperature`) | JSON (SDK handles it) |
| **Bedrock `InvokeModel`** | The model's own body (next table) | `application/json` |
| **SageMaker JumpStart LLM endpoint** (`invoke_endpoint`) | `inputs` (templated text) + `parameters` (`max_new_tokens`, `temperature`, `top_p`) | `application/json` |
| **SageMaker built-in tabular algorithms** (XGBoost, etc.) | One row of comma-separated feature values, **in training order** | `text/csv` |

## Bedrock `InvokeModel` by model family

| Family | Prompt field | Max-output field | Output text at | Prompt format notes |
|---|---|---|---|---|
| **Anthropic Claude** (Messages API) | `messages` (+ `system`); needs `anthropic_version: "bedrock-2023-05-31"` | `max_tokens` (required) | `content[0].text` | Alternating `user`/`assistant`; content is a string or blocks (text, image) |
| **Amazon Nova** | `messages` + `system` with `schemaVersion: "messages-v1"` | `inferenceConfig.maxTokens` | `output.message.content[0].text` | Same schema as Converse |
| **Meta Llama** | `prompt` | `max_gen_len` (also `temperature`, `top_p`) | `generation` | Llama chat template: `<|begin_of_text|><|start_header_id|>user<|end_header_id|> ... <|eot_id|><|start_header_id|>assistant<|end_header_id|>` |
| **Mistral** | `prompt` | `max_tokens` (`stop`, `temperature`, `top_p`, `top_k`) | `outputs[0].text` | `<s>[INST] question [/INST]` |
| **Amazon Titan Text** | `inputText` | `textGenerationConfig.maxTokenCount` | `results[0].outputText` | Chat style: `"User: ...\nBot:"` |
| **Cohere Command R / R+** | `message` (+ `chat_history`, `documents`) | `max_tokens` (`p`, `k`) | `text` (+ `citations`) | `documents` enable grounded answers with citations |

Stop reason fields differ too: Llama and Mistral `stop` / `length`; Claude `stop_reason`; Titan `completionReason`; Cohere `finish_reason`; Converse `stopReason`.

## Embedding models (Bedrock `InvokeModel`)

All models, dimensions and limits: [Bedrock Embedding Models](../05-Vector-Stores-and-Embeddings/07-Bedrock-Embedding-Models.md). Request shapes:

- **Titan Text Embeddings V2**: `inputText` plus optional `dimensions` (1,024 / 512 / 256), `normalize`, `embeddingTypes`; returns `embedding`. Details: [Embeddings](../05-Vector-Stores-and-Embeddings/06-Embeddings-and-Index-Freshness.md).
- **Titan G1 Text**: only `inputText`, 1,536 dimensions.
- **Cohere Embed**: list of `texts` and an `input_type` (document vs query); 1,024 dimensions.

## SageMaker JumpStart LLM details

- Body: `{"inputs": "<templated prompt>", "parameters": {"max_new_tokens": 256, "temperature": 0.6, "top_p": 0.9}}`; the reply holds `generated_text`.
- You still apply the model's **chat template** to the prompt yourself (it is one text string).
- Some models (such as Meta Llama) require **accepting the model EULA** when deploying.
- Not a Bedrock shape: `prompt`/`max_gen_len` (Bedrock Llama) or `messages`/`inferenceConfig` (Converse) will be rejected by a JumpStart endpoint.

## SageMaker built-in algorithms (non-LLM)

- **Request types:** `text/csv` (comma-separated, **feature order must match training**), `application/json` (an `instances` list), `application/jsonlines`, `application/x-recordio-protobuf`; XGBoost accepts CSV (and libsvm).
- **Batch transform:** set `SplitType` from the input type (`Line` for CSV and JSON Lines, `None` for JSON and images); prefer **JSON Lines** responses.

## Model families at a glance

| Provider | Models | Typical strengths |
|---|---|---|
| **Anthropic** | Claude | Reasoning, coding, long context, tool use, vision |
| **Amazon** | Nova (text, multimodal), Titan (text, embeddings, image) | Cost-efficient general models; Nova multimodal; Titan embeddings for RAG |
| **Meta** | Llama | Open-weight general models; also on SageMaker JumpStart |
| **Mistral AI** | Mistral, Mixtral | Efficient open-weight models |
| **Cohere** | Command R/R+, Embed, Rerank | RAG with citations, multilingual embeddings, reranking |

Choosing a hosting option for any of them: [Choosing How to Host an FM](Choosing-How-to-Host-an-FM.md). The uniform API: [Converse vs InvokeModel](../02-Amazon-Bedrock/01-Core-and-APIs/01-Converse-and-InvokeModel.md).
