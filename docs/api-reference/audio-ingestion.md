---
title: Audio Ingestion API
description: Upload audio files to NovaPulse for processing and analysis.
---
 
# Audio Ingestion API
 
Upload an audio file to NovaPulse to begin the processing pipeline.
The API accepts audio files in multiple formats, validates the upload,
stores the file securely, and returns a job ID for tracking.
 
---
 
## Endpoint
 
<span class="api-badge badge-post">POST</span> `/v1/audio/ingest`
 
---
 
## Prerequisites
 
- A valid API key with `audio:write` permission
- Audio file in a supported format (see [Supported formats](#supported-formats))
- File size must not exceed 500 MB
 
---
 
## Request
 
Send the request as `multipart/form-data`.
 
### Form fields
 
| Field | Type | Required | Description |
|---|---|---|---|
| `file` | binary | Yes | The audio file to upload. |
| `language` | string | No | BCP-47 language tag. Default: `en-US`. |
| `pipeline` | string | No | Processing pipeline. One of: `transcription`, `full-analysis`. Default: `full-analysis`. |
| `metadata` | JSON string | No | Optional key-value pairs attached to the job (e.g. `{"agent_id": "AGT-42"}`). |
| `webhook_url` | string | No | HTTPS URL to notify when processing completes. |
 
### Pipeline values
 
| Value | What runs |
|---|---|
| `transcription` | Transcription and speaker diarization only. Faster and lower cost. |
| `full-analysis` | Transcription + diarization + sentiment analysis + summarization + PII detection. |
 
---
 
## Supported formats
 
| Format | Extension | Max sample rate |
|---|---|---|
| MP3 | `.mp3` | 48 kHz |
| WAV | `.wav` | 96 kHz |
| M4A | `.m4a` | 48 kHz |
| FLAC | `.flac` | 96 kHz |
| OGG | `.ogg` | 48 kHz |
 
---
 
## Request example
 
=== "cURL"
    ```bash
    curl -X POST https://api.novapulse.io/v1/audio/ingest \
      -H 'Authorization: Bearer nvp_live_abc123...' \
      -F 'file=@/path/to/call-recording.mp3' \
      -F 'pipeline=full-analysis' \
      -F 'language=en-US' \
      -F 'metadata={"agent_id": "AGT-42", "call_type": "support"}'
    ```
 
=== "Python"
    ```python
    import requests
    import json
 
    url = 'https://api.novapulse.io/v1/audio/ingest'
    headers = {'Authorization': 'Bearer nvp_live_abc123...'}
 
    with open('/path/to/call-recording.mp3', 'rb') as audio_file:
        files = {'file': ('call-recording.mp3', audio_file, 'audio/mpeg')}
        data = {
            'pipeline': 'full-analysis',
            'language': 'en-US',
            'metadata': json.dumps({'agent_id': 'AGT-42'})
        }
        response = requests.post(url, headers=headers, files=files, data=data)
 
    print(response.json())
    ```
 
---
 
## Response
 
Returns `202 Accepted` immediately. Processing happens asynchronously.
 
```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "status": "queued",
  "pipeline": "full-analysis",
  "created_at": "2025-04-15T14:32:00Z",
  "estimated_completion": "2025-04-15T14:34:00Z",
  "file": {
    "name": "call-recording.mp3",
    "size_bytes": 4823041,
    "duration_seconds": 312
  }
}
```
 
### Response fields
 
| Field | Type | Description |
|---|---|---|
| `job_id` | string | Unique identifier for this processing job. Use this to poll status. |
| `status` | string | Initial status. Always `queued` on creation. |
| `pipeline` | string | The pipeline requested. |
| `created_at` | string (ISO 8601) | Timestamp when the job was created. |
| `estimated_completion` | string (ISO 8601) | Estimated time when results will be available. |
| `file.name` | string | Original filename as uploaded. |
| `file.size_bytes` | integer | File size in bytes. |
| `file.duration_seconds` | integer | Audio duration detected at upload time. |
 
---
 
## Response codes
 
| Code | Meaning |
|---|---|
| `202 Accepted` | Upload successful. Job has been queued. |
| `400 Bad Request` | Missing required field or invalid parameter. |
| `401 Unauthorized` | Invalid or missing API key. |
| `413 Payload Too Large` | File exceeds 500 MB limit. |
| `415 Unsupported Media Type` | Audio format not supported. |
| `429 Too Many Requests` | Rate limit exceeded. See [Rate limits](../reference/rate-limits.md). |
 
---
 
## Polling for job status
 
After upload, poll `GET /v1/jobs/{job_id}` to check processing status:
 
```bash
curl https://api.novapulse.io/v1/jobs/job_9f3c2a1b4e7d \
  -H 'Authorization: Bearer nvp_live_abc123...'
```
 
Job status transitions: `queued` → `processing` → `completed` or `failed`
 
---
 
## Related
 
- [Transcription API](transcription.md) — Retrieve transcript results
- [Sentiment Analysis API](sentiment-analysis.md) — Retrieve sentiment data
- [Getting Started tutorial](../tutorials/getting-started.md)



