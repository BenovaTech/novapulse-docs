---
title: API Reference
description: Complete reference documentation for the NovaPulse REST API.
---

# API Reference

The NovaPulse API is a REST API that accepts JSON request bodies and returns
JSON responses. All requests must be authenticated using an API key.

**Base URL:** `https://api.novapulse.io/v1`

---

## Authentication

All endpoints require a Bearer token in the `Authorization` header:

```bash
Authorization: Bearer nvp_live_YOUR_API_KEY
```

See [Authentication](authentication.md) for full details.

---

## Endpoints

| Endpoint | Method | Description |
|---|---|---|
| [`/v1/audio/ingest`](audio-ingestion.md) | POST | Upload an audio file for processing |
| [`/v1/jobs/{job_id}`](audio-ingestion.md#polling-for-job-status) | GET | Check processing job status |
| [`/v1/transcriptions/{job_id}`](transcription.md) | GET | Retrieve transcript and diarization results |
| [`/v1/insights/{job_id}`](sentiment-analysis.md) | GET | Retrieve sentiment analysis and behavioral metrics |

---

## Request and response format

All request bodies must be sent as `multipart/form-data` (for file uploads)
or `application/json`. All responses are returned as `application/json`
unless an alternative format is requested via query parameter.

---

## Error format

All error responses follow a consistent structure:

```json
{
  "error": {
    "code": "invalid_api_key",
    "message": "The API key provided is invalid or has been revoked.",
    "status": 401
  }
}
```

See [Error codes](../reference/error-codes.md) for the full list.

---

## Rate limits

All endpoints are rate-limited per API key. See [Rate limits](../reference/rate-limits.md)
for thresholds and retry guidance.

---

## API versioning

The current stable version is `v1`. Breaking changes will not be introduced
without a version increment and a minimum 90-day deprecation notice.
