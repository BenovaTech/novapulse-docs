---
title: Error Codes
description: Complete reference for NovaPulse API error codes, causes, and resolution guidance.
---

# Error Codes

All NovaPulse API errors return a consistent JSON structure with an HTTP
status code, a machine-readable error code, and a human-readable message.

---

## Error response format

```json
{
  "error": {
    "code": "invalid_api_key",
    "message": "The API key provided is invalid or has been revoked.",
    "status": 401
  }
}
```

| Field | Type | Description |
|---|---|---|
| `code` | string | Machine-readable error identifier. Use this in your error handling logic. |
| `message` | string | Human-readable description of the error. |
| `status` | integer | The HTTP status code returned with this error. |

---

## Authentication errors

| HTTP status | Code | Cause | Resolution |
|---|---|---|---|
| `401` | `missing_api_key` | No `Authorization` header was sent. | Include `Authorization: Bearer YOUR_KEY` in every request. |
| `401` | `invalid_api_key` | The key is malformed or does not exist. | Verify the key in your dashboard under Settings > API Keys. |
| `401` | `expired_api_key` | The key has been revoked or expired. | Generate a new key in the dashboard. |
| `403` | `insufficient_permissions` | The key does not have the required scope for this endpoint. | Check the key's permission scope or generate a key with broader permissions. |

---

## Request errors

| HTTP status | Code | Cause | Resolution |
|---|---|---|---|
| `400` | `missing_required_field` | A required field was not included in the request. | Check the API reference for required fields and include them. |
| `400` | `invalid_parameter` | A parameter value is not valid (wrong type, out of range, or unsupported value). | Review accepted values in the endpoint's parameter table. |
| `400` | `invalid_language_code` | The `language` value is not a valid BCP-47 tag. | Use a supported language code such as `en-US`. |
| `413` | `file_too_large` | The uploaded file exceeds the 500 MB limit. | Split the audio file and upload in parts. |
| `415` | `unsupported_media_type` | The audio format is not supported. | Convert the file to MP3, WAV, M4A, FLAC, or OGG before uploading. |

---

## Job and resource errors

| HTTP status | Code | Cause | Resolution |
|---|---|---|---|
| `404` | `job_not_found` | The job ID does not exist or belongs to a different account. | Verify the job ID returned at upload time. |
| `404` | `job_not_ready` | The job is still processing. Results are not yet available. | Poll job status until `"status": "completed"` before retrieving results. |
| `403` | `pipeline_mismatch` | The requested insight type requires `full-analysis` but the job was processed with `transcription` only. | Re-upload the file with `pipeline=full-analysis`. |
| `409` | `job_already_exists` | A job with identical file content and metadata was submitted within the deduplication window (60 seconds). | Wait 60 seconds before resubmitting, or modify the metadata to differentiate the job. |

---

## Rate limit errors

| HTTP status | Code | Cause | Resolution |
|---|---|---|---|
| `429` | `rate_limit_exceeded` | The request rate for your API key exceeded the allowed limit. | Wait for the duration specified in the `Retry-After` header before retrying. See [Rate Limits](rate-limits.md). |
| `429` | `concurrent_jobs_exceeded` | Too many jobs are processing simultaneously for your account tier. | Wait for at least one job to complete before submitting new uploads. |

---

## Server errors

| HTTP status | Code | Cause | Resolution |
|---|---|---|---|
| `500` | `internal_error` | An unexpected error occurred on the NovaPulse server. | Retry the request with exponential backoff. If the issue persists, contact support with your job ID. |
| `503` | `service_unavailable` | The service is temporarily unavailable, usually due to maintenance. | Check the NovaPulse status page and retry after the maintenance window. |

---

## Retry guidance

For transient errors (429, 500, 503), use exponential backoff:

```python
import time
import requests

def request_with_retry(url, headers, max_retries=4):
    delay = 1
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers)
        if response.status_code == 429:
            retry_after = int(response.headers.get("Retry-After", delay))
            time.sleep(retry_after)
            delay *= 2
        elif response.status_code >= 500:
            time.sleep(delay)
            delay *= 2
        else:
            return response
    raise Exception("Max retries exceeded")
```

---

## Related

- [Rate Limits](rate-limits.md)
- [Authentication](../api-reference/authentication.md)
- [Audio Ingestion API](../api-reference/audio-ingestion.md)

