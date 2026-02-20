---
title: Transcription API
description: Retrieve speaker-attributed transcripts and diarization results for processed audio jobs.
---

# Transcription API

Retrieve the transcript generated from a processed audio file. Transcripts include
speaker-attributed segments with timestamps, confidence scores, and word-level
detail. Transcription is available after the job status is `completed`.

---

## Endpoint

<span class="api-badge badge-get">GET</span> `/v1/transcriptions/{job_id}`

---

## Prerequisites

- A valid API key with `transcription:read` permission
- A completed job ID from the [Audio Ingestion API](audio-ingestion.md)
- Job status must be `completed` — polling a job still in `processing` returns `404`

---

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_id` | string | Yes | The job ID returned by `POST /v1/audio/ingest`. |

---

## Query parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `format` | string | `json` | Response format. One of: `json`, `srt`, `vtt`, `txt`. |
| `speaker_labels` | boolean | `true` | Include speaker labels in the response. Set to `false` for raw transcript only. |
| `word_timestamps` | boolean | `false` | Include per-word start and end timestamps. Increases response size significantly. |
| `redact_pii` | boolean | `true` | Return the redacted transcript. Set to `false` to retrieve the unredacted version (requires `pii:read` permission). |

---

## Request example

=== "cURL"
    ```bash
    curl https://api.novapulse.io/v1/transcriptions/job_9f3c2a1b4e7d \
      -H "Authorization: Bearer nvp_live_abc123..."
    ```

=== "cURL — SRT format"
    ```bash
    curl "https://api.novapulse.io/v1/transcriptions/job_9f3c2a1b4e7d?format=srt" \
      -H "Authorization: Bearer nvp_live_abc123..."
    ```

=== "Python"
    ```python
    import requests

    job_id = "job_9f3c2a1b4e7d"
    url = f"https://api.novapulse.io/v1/transcriptions/{job_id}"
    headers = {"Authorization": "Bearer nvp_live_abc123..."}
    params = {"word_timestamps": True}

    response = requests.get(url, headers=headers, params=params)
    transcript = response.json()
    ```

---

## Response

Returns `200 OK` with the transcript object.

```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "status": "completed",
  "language": "en-US",
  "duration_seconds": 312,
  "speakers_detected": 2,
  "transcription_accuracy": 0.94,
  "segments": [
    {
      "id": 1,
      "speaker": "Agent",
      "start": 0.0,
      "end": 4.2,
      "text": "Thank you for calling support. How can I help you today?",
      "confidence": 0.98
    },
    {
      "id": 2,
      "speaker": "Customer",
      "start": 4.5,
      "end": 11.8,
      "text": "I need to cancel my subscription. The pricing just doesn't work for us.",
      "confidence": 0.96
    },
    {
      "id": 3,
      "speaker": "Agent",
      "start": 12.1,
      "end": 18.4,
      "text": "I'm sorry to hear that. Can I ask what specifically isn't working for you?",
      "confidence": 0.97
    }
  ],
  "summary": "Customer called to cancel subscription due to pricing concerns. Agent attempted retention. No resolution reached — escalation recommended.",
  "pii_redacted": true,
  "created_at": "2025-04-15T14:32:00Z",
  "completed_at": "2025-04-15T14:33:47Z"
}
```

---

## Response fields

| Field | Type | Description |
|---|---|---|
| `job_id` | string | The job identifier. |
| `status` | string | Always `completed` when transcript is available. |
| `language` | string | BCP-47 language tag detected or specified at upload. |
| `duration_seconds` | integer | Total audio duration in seconds. |
| `speakers_detected` | integer | Number of distinct speakers identified by diarization. |
| `transcription_accuracy` | float | Aggregate confidence score across all segments (0.0–1.0). |
| `segments` | array | Ordered list of transcript segments. See [Segment object](#segment-object). |
| `summary` | string | AI-generated conversation summary. Present only when `pipeline=full-analysis`. |
| `pii_redacted` | boolean | Whether PII has been redacted in this response. |
| `created_at` | string | ISO 8601 timestamp when the job was created. |
| `completed_at` | string | ISO 8601 timestamp when processing finished. |

---

## Segment object

Each object in the `segments` array represents a continuous speech segment
attributed to a single speaker.

| Field | Type | Description |
|---|---|---|
| `id` | integer | Segment index, starting at 1. |
| `speaker` | string | Speaker label. Values: `Agent`, `Customer`, or `Speaker N` for unlabeled speakers. |
| `start` | float | Segment start time in seconds from the beginning of the audio. |
| `end` | float | Segment end time in seconds. |
| `text` | string | Transcribed text for this segment. PII is redacted if `pii_redacted` is `true`. |
| `confidence` | float | Model confidence score for this segment (0.0–1.0). Segments below 0.75 may contain errors. |

---

## Export formats

| Format | Use case |
|---|---|
| `json` | Programmatic processing, integration with downstream systems |
| `srt` | Subtitle files for video players |
| `vtt` | Web Video Text Tracks — for use in HTML5 `<video>` elements |
| `txt` | Plain text, no timestamps or speaker labels |

---

## Response codes

| Code | Meaning |
|---|---|
| `200 OK` | Transcript retrieved successfully. |
| `401 Unauthorized` | Invalid or missing API key. |
| `403 Forbidden` | Insufficient permissions. Check your key's scope. |
| `404 Not Found` | Job ID not found, or job is still processing. |
| `429 Too Many Requests` | Rate limit exceeded. |

---

!!! note "Speaker label accuracy"
    Speaker labels (`Agent`, `Customer`) are assigned automatically based on
    conversation patterns. For calls with more than two speakers, labels appear
    as `Speaker 1`, `Speaker 2`, etc. Speaker enrollment for higher accuracy
    is planned for v1.2.

---

## Related

- [Audio Ingestion API](audio-ingestion.md) — Upload audio and get a job ID
- [Sentiment Analysis API](sentiment-analysis.md) — Retrieve sentiment data for the same job
- [Upload & Analyze tutorial](../tutorials/upload-and-analyze.md)
- [Error codes](../reference/error-codes.md)

