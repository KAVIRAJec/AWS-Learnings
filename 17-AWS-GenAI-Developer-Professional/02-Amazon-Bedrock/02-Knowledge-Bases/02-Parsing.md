# Knowledge Bases — Parsing

Parsing extracts content from raw files before chunking; content lost here never reaches the index.

## Parser Options

| Parser | Handles | Cost | Notes |
|---|---|---|---|
| Bedrock default parser | Text only: .txt .md .html .doc/.docx .xls/.xlsx .pdf | Free | Drops figures/charts/tables-as-images |
| Bedrock Data Automation | Multimodal (PDF figures/charts/tables, .jpeg, .png) | Per page / per image | No prompt needed |
| Foundation model parser | Same multimodal set | Input + output tokens | Customizable parsing prompt; total file size across files ≤ 100 GB |

- Choosing BDA or FM parser applies to **all** PDFs in the data source, even text-only ones — you are charged for all of them.
- Extracted figures/tables can be stored to an S3 destination and returned in retrieval responses / source attribution.
