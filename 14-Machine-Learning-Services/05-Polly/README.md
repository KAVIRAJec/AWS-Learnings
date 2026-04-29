## Amazon Polly

Amazon Polly is a managed **text-to-speech (TTS)** service that converts text into lifelike speech using deep learning — enabling apps to talk.

**Voice Types:**
- **Standard Voices**: Concatenative synthesis — fast, cost-effective, slightly robotic.
- **Neural Voices (NTTS)**: Deep learning-based — significantly more natural and human-like. Higher quality, slightly higher cost.
- 60+ voices across 20+ languages — each language has multiple voice options (male/female, regional accents).

**SSML (Speech Synthesis Markup Language):**
- XML-based markup that gives fine-grained control over speech output.
- Common SSML tags:

| Tag | Effect |
|-----|--------|
| `<break time="1s"/>` | Add a pause of specified duration |
| `<emphasis level="strong">word</emphasis>` | Stress a word |
| `<say-as interpret-as="spell-out">AWS</say-as>` | Spell out acronyms letter by letter |
| `<prosody rate="slow" pitch="low">text</prosody>` | Adjust speaking rate and pitch |
| `<lang xml:lang="fr-FR">Bonjour</lang>` | Switch language mid-speech |

**Output Formats:**
- **MP3**: Standard compressed audio — most common for playback.
- **OGG Vorbis**: Open-source compressed format.
- **PCM**: Uncompressed raw audio — for telephony and real-time streaming.

**Lexicons:**
- Custom pronunciation dictionaries — teach Polly how to pronounce specific words or abbreviations.
- Format: PLS (Pronunciation Lexicon Specification) XML.
- Example: Map `AWS` → "Amazon Web Services", `SQL` → "sequel".
- Stored per region, referenced by name at synthesis time.

**Streaming:**
- **Real-time streaming**: Polly can stream audio chunks as it generates — reduces latency for interactive applications.
- Audio streamed directly from the API response (no S3 needed for small requests).

**Use cases:** Audiobooks and e-learning narration, accessibility (screen readers, voice interfaces), voiceovers for video content, real-time voice responses in apps and IVR systems, podcast generation.
