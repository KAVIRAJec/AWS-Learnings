# Embedding Models on Amazon Bedrock

An embedding model turns content into a vector so similar things sit close together. On Bedrock the models differ by **what they accept** (text only, or text + images + video + audio), **how many dimensions** they output, and **which vector types** they return. Pick one model per index and keep it (why: [Embeddings and Index Freshness](06-Embeddings-and-Index-Freshness.md)).

## The models

| Provider | Model (ID) | Input | Dimensions | Vector types | Key facts |
|---|---|---|---|---|---|
| **Amazon** | **Titan Text Embeddings V2** (`amazon.titan-embed-text-v2:0`) | Text | **1,024** (default), 512, 256 | float, binary | Up to 8,192 tokens / 50,000 characters; optimized for English with multilingual support; optional `normalize`; for RAG, search, reranking, classification |
| Amazon | **Titan Embeddings G1 – Text** (`amazon.titan-embed-text-v1`) | Text | **1,536** | float | Older text model; no inference parameters |
| Amazon | **Titan Multimodal Embeddings G1** (`amazon.titan-embed-image-v1`) | Text, image, or both | **1,024** (default), 384, 256 | float | Text up to 256 tokens (English), image up to 25 MB and 2048×2048; shared text/image space for image search; can be fine-tuned with image-text pairs |
| Amazon | **Nova Multimodal Embeddings** (`amazon.nova-2-multimodal-embeddings-v1:0`) | Text, documents, images, **video, audio** | **3,072**, 1,024, 384, 256 | float | One unified space for cross-modal retrieval; 8K tokens or 30 s of video/audio per input; sync `InvokeModel`, async `StartAsyncInvoke` with segmentation of long media |
| **Cohere** | **Embed English v3** (`cohere.embed-english-v3`) | Text (v3 also one image per call) | **1,024** | float, int8, uint8, binary, ubinary | 512 tokens per text, up to 96 texts per call; needs `input_type` |
| Cohere | **Embed Multilingual v3** (`cohere.embed-multilingual-v3`) | Text (image) | **1,024** | same | Use for mixed-language content |
| Cohere | **Embed v4** (`cohere.embed-v4`) | Text, images, **interleaved** text+image pages | **1,536** (default), 1,024, 512, 256 | float, int8, uint8, binary, ubinary | About 128K tokens per document; up to 96 items per call; images up to 5 MB; good for PDF pages as images |
| **TwelveLabs** | **Marengo Embed 2.7** (`twelvelabs.marengo-embed-2-7-v1:0`) | Video, audio, image, text | not stated in the docs I read | n/a | Text up to 77 tokens, images 5 MB, video/audio up to 2 GB from S3; `InvokeModel` for text/image queries, `StartAsyncInvoke` for video/audio; embedding options `visual-text`, `visual-image`, `audio` |

(A newer Marengo 3.0 also exists; details not checked.)

## Rules that decide questions

- **Dimension must match the vector index.** The index `dimension` equals the model's output size ([Store Options](03-KB-Vector-Store-Options.md)). Smaller dimensions or binary/int8 vectors save storage and speed search, at some accuracy cost.
- **Same model for documents and queries.** Never mix models in one index.
- **Cohere `input_type`:** embed the corpus with `search_document` and questions with `search_query` (also `classification`, `clustering`).
- **Throttling:** Bedrock embedding models are limited by **requests per minute**, not tokens per minute.
- **Long text:** even with large limits, split documents into logical chunks for retrieval ([Chunking](../02-Amazon-Bedrock/02-Knowledge-Bases/01-Chunking.md)).
- **Knowledge Bases** support the Titan, Cohere and Nova multimodal embedding models listed in the KB docs; not every model here is a KB option.

## Quick pick

| Need | Model |
|---|---|
| English text RAG, adjustable size | Titan Text Embeddings V2 |
| Many languages | Cohere Embed Multilingual v3 (or Embed v4) |
| Search images or PDF pages with text | Cohere Embed v4, Titan Multimodal, or Nova Multimodal |
| Video and audio search | Nova Multimodal Embeddings or TwelveLabs Marengo |
| Smallest storage | Smaller dimensions, or binary/int8 vectors where the vector store supports them |
