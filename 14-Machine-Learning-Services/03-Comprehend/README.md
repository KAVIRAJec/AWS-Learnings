## Amazon Comprehend

Amazon Comprehend is a managed **Natural Language Processing (NLP)** service that uses ML to extract meaning, structure, and insights from text — no NLP expertise required.

**Key Concepts:**
- **Entity Recognition**: Detect and classify named entities — PERSON, ORGANIZATION, LOCATION, DATE, QUANTITY, COMMERCIAL_ITEM, etc. — with confidence scores.
- **Key Phrase Extraction**: Identify the most significant noun phrases in text — captures the main topics.
- **Sentiment Analysis**: Classify the overall tone as POSITIVE, NEGATIVE, NEUTRAL, or MIXED — returns a confidence score for each label.
- **Targeted Sentiment**: Determine sentiment toward specific entities within the same document (e.g., "the food was great" → FOOD: POSITIVE, "service was poor" → SERVICE: NEGATIVE).
- **Language Detection**: Identify the dominant language in a document — supports 100+ languages.
- **Syntax Analysis**: Tokenize text and label parts of speech (noun, verb, adjective) — useful for grammar-based filtering.
- **PII Detection**: Identify Personally Identifiable Information — names, addresses, phone numbers, credit cards, SSNs. Can **redact** PII from documents automatically.

**Custom Models:**
- **Custom Entity Recognition**: Train Comprehend to detect your own entity types (product SKUs, internal codes, medical terms) using annotated training data (CSV or augmented manifests).
- **Custom Classification**: Train a multi-class or multi-label document classifier to categorize documents into your own categories (support ticket routing, content tagging).

**Amazon Comprehend Medical:**
- Specialized NLP for **clinical and biomedical text** — recognizes medical entities: diagnoses (ICD-10 codes), medications, dosages, procedures, anatomy, and test results.
- **PHI (Protected Health Information)** detection and redaction — HIPAA-eligible service.
- **ICD-10-CM and RxNorm linking**: Maps detected entities to standardized medical ontologies.
- Separate service endpoint from standard Comprehend.

**Processing Modes:**
- **Synchronous**: Single-document APIs (`DetectEntities`, `DetectSentiment`) — immediate response, up to 100 KB per request.
- **Asynchronous (Batch)**: Submit jobs pointing to S3 (`StartEntitiesDetectionJob`) — results written back to S3. Best for large document volumes.

**Use cases:** Customer feedback and review analysis, call center conversation analytics, document classification and routing, medical record processing, PII redaction in data pipelines, support ticket categorization.
