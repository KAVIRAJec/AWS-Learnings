## Amazon Transcribe

Amazon Transcribe is a managed **automatic speech recognition (ASR)** service that converts audio and video into accurate text transcripts.

**Key Features:**
- **Batch Transcription**: Submit audio/video files from S3 — returns a JSON transcript with word-level timestamps and confidence scores.
- **Real-time Streaming**: Low-latency transcription via WebSocket or HTTP/2 streaming — for live audio (calls, meetings, broadcasts).
- **Speaker Identification (Diarization)**: Distinguish and label different speakers in a conversation (Speaker 1, Speaker 2). Specify the expected number of speakers.
- **Automatic Punctuation & Casing**: Adds periods, commas, question marks, and proper capitalization to improve readability.
- **Language Identification**: Auto-detect the spoken language — can identify from a list of specified candidates.
- **Word Confidence Scores**: Each transcribed word includes a confidence score — filter or flag low-confidence sections.
- **Channel Identification**: Separate transcripts per audio channel (e.g., agent vs. customer in a stereo call recording).

**Customization:**
- **Custom Vocabulary**: Add domain-specific words, product names, acronyms, and proper nouns — improves accuracy without retraining.
- **Custom Language Model (CLM)**: Fine-tune transcription for a specific domain (medical, legal, technical) by training on your own text corpus. Better accuracy than vocabulary alone for specialized language.
- **Vocabulary Filters**: Automatically mask, remove, or tag specific words (profanity, competitor names) from the transcript.

**PII Redaction:**
- Automatically identify and redact sensitive information (SSNs, phone numbers, credit card numbers, dates of birth) in the transcript — replaced with `[PII]`.
- Available in both batch and streaming modes.

**Amazon Transcribe Medical:**
- Specialized ASR trained on **clinical and medical vocabulary** — significantly higher accuracy for healthcare audio.
- **Dictation mode**: Single speaker (physician dictating notes).
- **Conversation mode**: Multiple speakers (doctor-patient conversation).
- PHI identification and redaction — HIPAA-eligible.
- Recognizes medical terms, drug names, and anatomical references accurately.

**Supported Formats:** MP3, MP4, WAV, FLAC, AMR, OGG, WebM.

**Use cases:** Call center quality monitoring and analytics, meeting transcription, closed captions and subtitles for media, voice search indexing, medical documentation and clinical note generation.
