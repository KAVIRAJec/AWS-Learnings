## Amazon Rekognition

Amazon Rekognition is a managed **computer vision service** that provides pre-trained and customizable image and video analysis — no ML expertise required. It supports **PII detection and redaction** to automatically identify and mask personally identifiable information in images and videos.

**Image Analysis:**
- **Object & Scene Detection**: Identify thousands of objects (car, dog, beach), scenes, and activities with confidence scores.
- **Facial Analysis**: Detect faces and analyze attributes — age range, emotions (happy, sad, angry), gender, eye glasses, face pose, smile. Does **not** identify who the person is.
- **Facial Recognition**: Compare faces against a stored collection — identify or verify specific persons. Must first index faces into a **Collection**.
- **Text in Image (OCR)**: Detect and extract printed and handwritten text from images.
- **Content Moderation**: Identify explicit, unsafe, or inappropriate content — returns confidence scores. Supports human review workflows via **Amazon Augumented A2I**.
- **Celebrity Recognition**: Identify well-known public figures in images and video.
- **Custom Labels**: Train Rekognition to detect your own objects (products, logos, defects) using your labeled images — no ML expertise needed.

**Video Analysis:**
- **Label Detection**: Detect objects, scenes, and activities throughout a video with timestamps.
- **Face Search**: Track and identify faces across all frames of a video.
- **Activity Detection**: Detect specific actions (sports plays, package handling, unsafe behavior).
- **Segment Detection**: Identify chapters, credits, slates, and black frames — used for ad insertion and content cataloging.
- **Content Moderation**: Flag explicit content in video at specific timestamps.
- Video analysis is **asynchronous** — start a job, poll for completion, retrieve results from S3.

**Rekognition Collections:**
- A server-side container for indexed face vectors — used for facial recognition at scale.
- `IndexFaces` → stores face embeddings. `SearchFacesByImage` → match against stored faces.
- Supports millions of faces — used for employee badge verification, security access, and deduplication.

**Rekognition Streaming Video:**
- Analyze live video streams from **Kinesis Video Streams** in real time.
- Use case: Live surveillance, real-time alerts when a specific person is detected.

**Use cases:** Content moderation for UGC platforms, identity verification, workplace safety monitoring, media asset tagging, retail foot traffic analysis, smart building access control.
