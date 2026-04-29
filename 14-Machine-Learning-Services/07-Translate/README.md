## Amazon Translate

Amazon Translate is a managed **neural machine translation (NMT)** service that delivers fast, high-quality language translation using deep learning — understands context rather than translating word-for-word.

**Key Concepts:**
- Supports **75+ language pairs** — bidirectional translation between all supported languages.
- **Automatic Source Language Detection**: No need to specify the source language — Translate detects it automatically.
- **Real-time**: `TranslateText` API — synchronous, up to 10,000 bytes per request.
- **Batch**: `StartTextTranslationJob` — input from S3, output written back to S3. Supports TXT, HTML, DOCX, PPTX, XLSX. Preserves document formatting.

**Customization:**
- **Custom Terminology**: Upload a glossary (CSV or TMX) of terms that must always translate a specific way — brand names, product names, technical jargon. Translate never overrides these mappings.
- **Parallel Data**: Provide source + preferred translation sentence pairs to fine-tune the model to your domain's tone and style — more powerful than terminology alone for stylistic consistency.
- **Formality**: Control formal vs. informal register — supported for German, French, Japanese, Spanish, and select other languages.

**Use cases:** App and website localization, real-time multilingual customer support, translating large document repositories, e-commerce product catalog globalization.
