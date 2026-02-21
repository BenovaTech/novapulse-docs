---
title: Upload and analyze audio
description: Process multiple audio files, use metadata for filtering, and retrieve structured insights at scale.
---

# Upload and analyze audio

This tutorial covers the full NovaPulse analysis workflow in depth.
You will upload an audio file, configure the analysis pipeline,
retrieve the complete results: transcript, sentiment, behavioral
metrics, and AI summary, and learn how to use metadata to organize
and filter your data at scale.

**Time to complete:** approximately 20 minutes

---

## Prerequisites

Before you begin:

- [ ] Completed the [Getting Started tutorial](getting-started.md)
- [ ] A NovaPulse API key set as an environment variable
- [ ] At least one audio file in MP3 or WAV format
- [ ] cURL or Python installed

If you haven't set your API key as an environment variable yet:

=== "macOS / Linux"
    ```bash
    export NOVAPULSE_API_KEY=nvp_live_YOUR_KEY_HERE
    ```

=== "Windows (PowerShell)"
    ```powershell
    $env:NOVAPULSE_API_KEY = 'nvp_live_YOUR_KEY_HERE'
    ```

---

## Step 1: Upload with full metadata

Metadata is key-value data you attach to a job at upload time. It has no
effect on processing, but it makes your data filterable and traceable
in the dashboard and via the API. It is essential for production use.

Upload your audio file with a rich metadata payload:

```bash
curl -X POST https://api.novapulse.io/v1/audio/ingest \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY" \
  -F 'file=@support-call-april.mp3' \
  -F 'pipeline=full-analysis' \
  -F 'language=en-US' \
  -F 'metadata={
    "agent_id": "AGT-42",
    "team": "customer-support",
    "call_type": "inbound",
    "queue": "billing",
    "crm_ticket_id": "TKT-9821",
    "region": "emea"
  }' \
  -F 'webhook_url=https://your-system.io/novapulse/webhook'
```

You should receive:

```json
{
  "job_id": "job_7a2d9c4f1e8b",
  "status": "queued",
  "pipeline": "full-analysis",
  "created_at": "2025-04-15T09:14:22Z",
  "estimated_completion": "2025-04-15T09:16:30Z",
  "file": {
    "name": "support-call-april.mp3",
    "size_bytes": 6241088,
    "duration_seconds": 389
  }
}
```

Save the `job_id`. You will use it throughout this tutorial.

!!! tip "Metadata best practices"
    Use consistent key names across all uploads, this makes
    filtering reliable. Treat metadata like database columns:
    define a schema and stick to it. Common fields: `agent_id`,
    `team`, `call_type`, `queue`, `crm_ticket_id`, `region`.

---

## Step 2: Use a webhook instead of polling (recommended)

In the [Getting Started tutorial](getting-started.md) you polled the
job status endpoint. For production integrations, webhooks are better:
your system receives a notification the moment processing completes,
with no need for repeated requests.

If you passed `webhook_url` in Step 1, NovaPulse will POST this
payload to your endpoint when the job finishes:

```json
{
  "event": "job.completed",
  "job_id": "job_7a2d9c4f1e8b",
  "status": "completed",
  "pipeline": "full-analysis",
  "completed_at": "2025-04-15T09:16:18Z"
}
```

Your webhook handler should respond with `200 OK` within 5 seconds.
NovaPulse retries failed webhook deliveries up to 3 times with
exponential backoff.

If you don't have a webhook endpoint available during development,
continue to use polling:

```bash
curl https://api.novapulse.io/v1/jobs/job_7a2d9c4f1e8b \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

Wait until `"status": "completed"` before proceeding.

---

## Step 3: Retrieve the full transcript

```bash
curl https://api.novapulse.io/v1/transcriptions/job_7a2d9c4f1e8b \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

The response includes speaker-attributed segments with confidence scores:

```json
{
  "job_id": "job_7a2d9c4f1e8b",
  "language": "en-US",
  "duration_seconds": 389,
  "speakers_detected": 2,
  "transcription_accuracy": 0.95,
  "segments": [
    {
      "id": 1,
      "speaker": "Agent",
      "start": 0.0,
      "end": 5.1,
      "text": "Thank you for calling billing support. My name is Sarah, how can I help?",
      "confidence": 0.99
    },
    {
      "id": 2,
      "speaker": "Customer",
      "start": 5.4,
      "end": 14.7,
      "text": "Hi Sarah, I got charged twice this month and nobody seems to know why.",
      "confidence": 0.97
    }
  ],
  "summary": "Customer reported a duplicate billing charge. Agent investigated the account, confirmed the error, and initiated a refund. Resolved — refund processing within 3-5 business days.",
  "pii_redacted": true
}
```

### Export as SRT (subtitle format)

For integrations with video players, training platforms, or compliance
archives, export the transcript as SRT:

```bash
curl "https://api.novapulse.io/v1/transcriptions/job_7a2d9c4f1e8b?format=srt" \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY" \
  -o transcript.srt
```

---

## Step 4: Retrieve sentiment analysis

```bash
curl "https://api.novapulse.io/v1/insights/job_7a2d9c4f1e8b?type=sentiment" \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

```json
{
  "job_id": "job_7a2d9c4f1e8b",
  "sentiment": {
    "overall_label": "positive",
    "overall_score": 0.42,
    "trend": "improving",
    "segments": [
      {
        "segment_id": 2,
        "speaker": "Customer",
        "start": 5.4,
        "end": 14.7,
        "label": "negative",
        "score": -0.71
      },
      {
        "segment_id": 4,
        "speaker": "Customer",
        "start": 47.2,
        "end": 58.9,
        "label": "positive",
        "score": 0.84
      }
    ]
  }
}
```

Notice the `trend` field is `improving`. The customer started
frustrated but ended satisfied. This is a high-value signal for
agent coaching: the agent successfully de-escalated the situation.

---

## Step 5: Retrieve behavioral metrics

```bash
curl "https://api.novapulse.io/v1/insights/job_7a2d9c4f1e8b?type=behavioral" \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

```json
{
  "job_id": "job_7a2d9c4f1e8b",
  "behavioral": {
    "silence_ratio": 0.06,
    "interruption_count": 1,
    "agent_talk_ratio": 0.55,
    "customer_talk_ratio": 0.39,
    "average_response_time_seconds": 1.1
  },
  "recommendations": [
    "Sentiment trend improved significantly — flag for positive agent coaching example.",
    "Low interruption count indicates calm, professional interaction.",
    "Agent talk ratio slightly high — monitor for over-explanation patterns."
  ]
}
```

---

## Step 6: Retrieve everything in one request

Instead of calling each endpoint separately, use `type=all` to
retrieve sentiment, behavioral metrics, summary, and recommendations
in a single response:

```bash
curl "https://api.novapulse.io/v1/insights/job_7a2d9c4f1e8b?type=all" \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

This reduces latency for dashboards and reporting pipelines that
need the full picture at once.

---

## Step 7: Filter by customer sentiment only

When building agent performance dashboards, you often only care about
the customer's sentiment, not the agent's. Use the `speaker` filter:

```bash
curl "https://api.novapulse.io/v1/insights/job_7a2d9c4f1e8b?type=sentiment&speaker=Customer" \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```

This returns only the customer's sentiment segments, making it
straightforward to calculate a customer satisfaction score (CSAT)
from actual conversation data rather than post-call surveys.

---

## What you built

- [x] Uploaded audio with structured metadata for production-grade traceability
- [x] Configured webhook notification instead of polling
- [x] Retrieved a speaker-attributed transcript with confidence scores
- [x] Exported the transcript in SRT format
- [x] Retrieved per-segment sentiment with trend analysis
- [x] Retrieved behavioral metrics and AI-generated recommendations
- [x] Retrieved all insights in a single request
- [x] Filtered sentiment by speaker for CSAT analysis

---

## Handling errors in production

Two errors you will commonly encounter:

**Job still processing - 404:**

```json
{
  "error": {
    "code": "job_not_ready",
    "message": "The job is still processing. Retry after the estimated completion time.",
    "status": 404
  }
}
```

Always check job status before retrieving results. Do not assume
a job is complete based on elapsed time alone.

**Pipeline mismatch - 403:**

```json
{
  "error": {
    "code": "pipeline_mismatch",
    "message": "Sentiment analysis requires pipeline=full-analysis. This job was processed with pipeline=transcription.",
    "status": 403
  }
}
```

Sentiment and behavioral data are only available for jobs processed
with `pipeline=full-analysis`. If you need to re-process, upload
the file again with the correct pipeline.

---

!!! note "Re-processing audio"
    NovaPulse does not currently support re-processing an existing job
    with a different pipeline. Upload the file again with
    `pipeline=full-analysis` to get sentiment and behavioral data
    for a file previously processed with `pipeline=transcription`.

---

## Next steps

- [Sentiment Analysis API reference](../api-reference/sentiment-analysis.md) —
  full response schema and parameter reference
- [Transcription API reference](../api-reference/transcription.md) —
  export formats and word-level timestamps
- [Vector Search concepts](../concepts/vector-search.md) —
  how to search across transcripts by meaning
- [RAG Pipeline](../concepts/rag-pipeline.md) —
  how NovaPulse answers natural language questions about your data

