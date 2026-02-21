---
title: Sentiment Analysis API
description: Retrieve per-segment and overall sentiment scores for a processed audio job.
---

# Sentiment Analysis API

Retrieve sentiment analysis results for a completed audio job. NovaPulse's
sentiment engine scores each transcript segment on a continuous scale and
produces an overall call sentiment classification, trend data, and
behavioral signals such as silence ratio and interruption count.

Sentiment analysis is available when the job was processed with
`pipeline=full-analysis`.

---

## Endpoint

<span class="api-badge badge-get">GET</span> `/v1/insights/{job_id}`

---

## Prerequisites

- A valid API key with `insights:read` permission
- A completed job processed with `pipeline=full-analysis`

---

## Path parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `job_id` | string | Yes | The job ID returned by `POST /v1/audio/ingest`. |

---

## Query parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `type` | string | `all` | Insight type to return. One of: `sentiment`, `behavioral`, `summary`, `all`. |
| `speaker` | string | — | Filter results to a specific speaker. Values: `Agent`, `Customer`. |
| `granularity` | string | `segment` | Sentiment resolution. One of: `segment`, `minute`, `call`. |

---

## Request example

=== "cURL sentiment only"
    ```bash
    curl "https://api.novapulse.io/v1/insights/job_9f3c2a1b4e7d?type=sentiment" \
      -H "Authorization: Bearer nvp_live_abc123..."
    ```

=== "cURL customer sentiment only"
    ```bash
    curl "https://api.novapulse.io/v1/insights/job_9f3c2a1b4e7d?type=sentiment&speaker=Customer" \
      -H "Authorization: Bearer nvp_live_abc123..."
    ```

=== "Python"
    ```python
    import requests

    job_id = "job_9f3c2a1b4e7d"
    url = f"https://api.novapulse.io/v1/insights/{job_id}"
    headers = {"Authorization": "Bearer nvp_live_abc123..."}
    params = {"type": "all", "speaker": "Customer"}

    response = requests.get(url, headers=headers, params=params)
    insights = response.json()

    print(f"Overall sentiment: {insights['sentiment']['overall_label']}")
    print(f"Score: {insights['sentiment']['overall_score']}")
    ```

---

## Response

Returns `200 OK` with the full insights object.

```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "pipeline": "full-analysis",
  "sentiment": {
    "overall_label": "negative",
    "overall_score": -0.68,
    "trend": "declining",
    "segments": [
      {
        "segment_id": 1,
        "speaker": "Agent",
        "start": 0.0,
        "end": 4.2,
        "label": "neutral",
        "score": 0.05
      },
      {
        "segment_id": 2,
        "speaker": "Customer",
        "start": 4.5,
        "end": 11.8,
        "label": "negative",
        "score": -0.82
      },
      {
        "segment_id": 3,
        "speaker": "Agent",
        "start": 12.1,
        "end": 18.4,
        "label": "neutral",
        "score": 0.12
      }
    ]
  },
  "behavioral": {
    "silence_ratio": 0.08,
    "interruption_count": 3,
    "agent_talk_ratio": 0.52,
    "customer_talk_ratio": 0.40,
    "average_response_time_seconds": 1.4
  },
  "summary": "Customer called to cancel subscription due to pricing concerns. Agent attempted retention. No resolution reached — escalation recommended.",
  "recommendations": [
    "Escalate to retention specialist — high churn risk detected.",
    "Review pricing communication — customer unaware of discount options.",
    "Agent response time within acceptable range."
  ]
}
```

---

## Response fields

### Top-level

| Field | Type | Description |
|---|---|---|
| `job_id` | string | The job identifier. |
| `pipeline` | string | The pipeline used. Sentiment data requires `full-analysis`. |
| `sentiment` | object | Sentiment analysis results. See [Sentiment object](#sentiment-object). |
| `behavioral` | object | Behavioral metrics. See [Behavioral object](#behavioral-object). |
| `summary` | string | AI-generated call summary. |
| `recommendations` | array | Actionable recommendations generated from the call analysis. |

---

### Sentiment object

| Field | Type | Description |
|---|---|---|
| `overall_label` | string | Overall call sentiment. One of: `positive`, `neutral`, `negative`. |
| `overall_score` | float | Aggregate sentiment score from -1.0 (most negative) to +1.0 (most positive). |
| `trend` | string | Sentiment direction across the call. One of: `improving`, `stable`, `declining`. |
| `segments` | array | Per-segment sentiment. See [Sentiment segment](#sentiment-segment). |

---

### Sentiment segment

| Field | Type | Description |
|---|---|---|
| `segment_id` | integer | References the `id` field in the transcript segment. |
| `speaker` | string | Speaker label for this segment. |
| `start` | float | Segment start time in seconds. |
| `end` | float | Segment end time in seconds. |
| `label` | string | Sentiment classification for this segment. |
| `score` | float | Sentiment score for this segment (-1.0 to +1.0). |

---

### Behavioral object

| Field | Type | Description |
|---|---|---|
| `silence_ratio` | float | Proportion of the call spent in silence (0.0–1.0). High values may indicate confusion or hold time. |
| `interruption_count` | integer | Number of times either speaker interrupted the other. |
| `agent_talk_ratio` | float | Proportion of speaking time attributed to the agent. |
| `customer_talk_ratio` | float | Proportion of speaking time attributed to the customer. |
| `average_response_time_seconds` | float | Mean time between one speaker finishing and the other beginning. |

---

## Sentiment score reference

| Score range | Label | Interpretation |
|---|---|---|
| +0.5 to +1.0 | `positive` | Customer is satisfied, engaged, or pleased |
| -0.4 to +0.5 | `neutral` | No strong emotional signal detected |
| -1.0 to -0.4 | `negative` | Customer is frustrated, dissatisfied, or at churn risk |

---

## Response codes

| Code | Meaning |
|---|---|
| `200 OK` | Insights retrieved successfully. |
| `400 Bad Request` | Invalid `type` or `granularity` parameter. |
| `401 Unauthorized` | Invalid or missing API key. |
| `403 Forbidden` | Insufficient permissions or job processed without `full-analysis`. |
| `404 Not Found` | Job ID not found or still processing. |
| `429 Too Many Requests` | Rate limit exceeded. |

---

!!! tip "Using sentiment for real-time alerting"
    In Phase 2, NovaPulse will expose sentiment scores via a streaming
    endpoint for live calls. Supervisors will be able to trigger interventions
    when a customer's sentiment score drops below a configurable threshold
    during an active conversation.

---

## Related

- [Transcription API](transcription.md) — Retrieve the full transcript
- [Audio Ingestion API](audio-ingestion.md) — Upload audio files
- [Getting Started tutorial](../tutorials/getting-started.md)
- [Glossary — Sentiment analysis](../reference/glossary.md#sentiment-analysis)

