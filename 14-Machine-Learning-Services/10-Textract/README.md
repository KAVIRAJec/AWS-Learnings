## Amazon Textract

Amazon Textract is a managed **document analysis** service that extracts text, handwriting, forms, and tables from scanned documents — goes beyond basic OCR by understanding document structure.

**Key Concepts:**
- **Text Detection**: Extract printed and handwritten text — returns bounding box coordinates per word/line.
- **Form Extraction**: Detect labeled fields and values — returns key-value pairs (e.g., `Patient Name: John Doe`).
- **Table Extraction**: Detect tables and extract cell contents preserving row/column structure.
- **Query-Based Extraction**: Ask plain-language questions (e.g., "What is the invoice total?") — Textract returns the answer directly without parsing full output.
- **Signature Detection**: Identify presence of a handwritten signature.
- **Sync**: `DetectDocumentText` / `AnalyzeDocument` — single-page JPEG/PNG only.
- **Async**: `StartDocumentAnalysis` — multi-page PDFs and TIFFs (up to 3,000 pages), results polled from S3.
- **A2I Integration**: Route low-confidence extractions to **Amazon Augmented AI (A2I)** for human review — only ambiguous results go to humans, reducing review volume.

**Use cases:** Loan/insurance form processing, medical record digitization, invoice and receipt extraction, KYC document verification, tax form automation.
