---
title: Rate Limits
description: NovaPulse API rate limits, headers, and strategies for high-volume integrations.
---

# Rate Limits

NovaPulse enforces rate limits per API key to ensure fair usage and
platform stability. All limits are measured on a rolling 60-second window.

---

## Limits by endpoint

| Endpoint | Method | Limit |
|---|---|---|
| `/v1/audio/ingest` | POST | 60 requests per minute |
| `/v1/jobs/{job_id}` | GET | 120 requests per minute |
| `/v1/transcriptions/{job_id}` | GET | 120 requests per minute |
| `/v1/insights/{job_id}` | GET | 120 requests per minute |

---

## Concurrent job limits

In addition to request rate limits, NovaPulse limits the number of jobs
that can be actively processing at the same time per account:

| Account tier | Concurrent jobs |
|---|---|
| Free trial | 3 |
| Starter | 10 |
| Professional | 50 |
| Enterprise | Custom |

---

## Rate limit response headers

Every API response includes headers showing your current rate limit status:

| Header | Description |
|---|---|
| `X-RateLimit-Limit` | The maximum number of requests allowed per minute for this endpoint. |
| `X-RateLimit-Remaining` | The number of requests remaining in the current window. |
| `X-RateLimit-Reset` | Unix timestamp when the current window resets. |
| `Retry-After` | Seconds to wait before retrying. Only present on `429` responses. |

### Example headers on a normal response

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 43
X-RateLimit-Reset: 1744714380
```

### Example headers on a rate-limited response

```
HTTP/1.1 429 Too Many Requests
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1744714380
Retry-After: 12
```

---

## Handling rate limit errors

When you receive a `429` response, read the `Retry-After` header and
wait that many seconds before retrying. Do not retry immediately.

```python
import time
import requests

def upload_with_rate_limit_handling(file_path, headers):
    with open(file_path, 'rb') as f:
        files = {'file': f}
        response = requests.post(
            'https://api.novapulse.io/v1/audio/ingest',
            headers=headers,
            files=files,
            data={'pipeline': 'full-analysis'}
        )

    if response.status_code == 429:
        retry_after = int(response.headers.get('Retry-After', 10))
        print(f"Rate limited. Retrying in {retry_after} seconds.")
        time.sleep(retry_after)
        return upload_with_rate_limit_handling(file_path, headers)

    return response.json()
```

---

## Strategies for high-volume integrations

If you are processing large batches of audio files, use these strategies
to stay within rate limits:

**Use webhooks instead of polling.** Polling job status repeatedly
consumes your request quota. Pass `webhook_url` at upload time and
let NovaPulse notify you when each job completes.

**Implement a queue.** Rather than uploading all files simultaneously,
queue uploads and process them at a controlled rate. A rate of
50 uploads per minute gives you headroom below the 60/minute limit.

**Monitor the remaining header.** Read `X-RateLimit-Remaining` on
every response and slow down proactively before hitting zero, rather
than reacting to `429` errors.

**Batch by priority.** For large backlog processing, separate urgent
calls (escalations, complaints) from routine processing and upload
high-priority files first.

---

## Requesting a limit increase

If your integration consistently requires higher limits, contact
NovaPulse support to discuss an Enterprise plan with custom rate limits
and concurrent job allocations tailored to your volume.

---

## Related

- [Error Codes](error-codes.md) — Full error reference including `429` handling
- [Audio Ingestion API](../api-reference/audio-ingestion.md)
- [Upload and Analyze tutorial](../tutorials/upload-and-analyze.md)

