---
title: Getting started
description: Make your first NovaPulse API call in under 10 minutes.
---
 
# Getting started
 
This tutorial walks you through uploading an audio file to NovaPulse
and retrieving a transcript with sentiment analysis. By the end,
you will have made your first successful API request and received
structured insights from a real audio file.
 
**Time to complete:** approximately 10 minutes
 
---
 
## Prerequisites
 
Before you begin:
 
- [ ] A NovaPulse account - [sign up free](https://app.novapulse.io/signup)
- [ ] An API key - see [Authentication](../api-reference/authentication.md)
- [ ] cURL installed (included on Windows 10+ and macOS)
- [ ] An audio file in MP3 or WAV format (minimum 10 seconds)
 
---
 
## Step 1: Set your API key as an environment variable
 
Store your key as an environment variable to avoid hardcoding it in
commands. Open your terminal and run:
 
=== "macOS / Linux"
    ```bash
    export NOVAPULSE_API_KEY=nvp_live_YOUR_KEY_HERE
    ```
 
=== "Windows (PowerShell)"
    ```powershell
    $env:NOVAPULSE_API_KEY = 'nvp_live_YOUR_KEY_HERE'
    ```
 
=== "Windows (Command Prompt)"
    ```cmd
    set NOVAPULSE_API_KEY=nvp_live_YOUR_KEY_HERE
    ```
 
---
 
## Step 2: Upload an audio file
 
Upload your file to the ingestion endpoint. Replace `call.mp3` with
the actual path to your audio file.
 
```bash
curl -X POST https://api.novapulse.io/v1/audio/ingest \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY" \
  -F 'file=@call.mp3' \
  -F 'pipeline=full-analysis'
```
 
You should see a response like this:
 
```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "status": "queued",
  "created_at": "2025-04-15T14:32:00Z",
  "estimated_completion": "2025-04-15T14:34:00Z"
}
```
 
Save the `job_id` value, you will use it in the next steps.
 
---
 
## Step 3: Check processing status
 
Audio processing is asynchronous. Poll the job status endpoint
until `status` is `completed`:
 
```bash
curl https://api.novapulse.io/v1/jobs/job_9f3c2a1b4e7d \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```
 
```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "status": "completed",
  "pipeline": "full-analysis",
  "completed_at": "2025-04-15T14:33:47Z"
}
```
 
!!! tip
    For production integrations, use a webhook instead of polling.
    Pass `webhook_url` in your upload request and NovaPulse will POST
    a notification to your endpoint when processing completes.
 
---
 
## Step 4: Retrieve the transcript
 
```bash
curl https://api.novapulse.io/v1/transcriptions/job_9f3c2a1b4e7d \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```
 
The response includes a speaker-attributed transcript:
 
```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "language": "en-US",
  "duration_seconds": 312,
  "segments": [
    {
      "speaker": "Agent",
      "start": 0.0,
      "end": 4.2,
      "text": "Thank you for calling support. How can I help you today?"
    },
    {
      "speaker": "Customer",
      "start": 4.5,
      "end": 11.8,
      "text": "I need to cancel my subscription. The pricing just doesn't work for us."
    }
  ]
}
```
 
---
 
## Step 5: Retrieve sentiment analysis
 
```bash
curl https://api.novapulse.io/v1/insights/job_9f3c2a1b4e7d?type=sentiment \
  -H "Authorization: Bearer $NOVAPULSE_API_KEY"
```
 
```json
{
  "job_id": "job_9f3c2a1b4e7d",
  "overall_sentiment": "negative",
  "sentiment_score": -0.68,
  "segments": [
    { "speaker": "Customer", "start": 4.5, "sentiment": "negative", "score": -0.82 },
    { "speaker": "Agent",    "start": 12.1, "sentiment": "neutral",  "score": 0.05 }
  ]
}
```
 
---
 
## What you built
 
- [x] Uploaded an audio file using the Ingestion API
- [x] Polled job status and confirmed completion
- [x] Retrieved a speaker-attributed transcript
- [x] Retrieved per-segment sentiment analysis
 
---
 
## Next steps
 
- [Upload & Analyze Audio tutorial](upload-and-analyze.md) — batch processing
  and metadata strategies
- [Sentiment Analysis API reference](../api-reference/sentiment-analysis.md)
- [Vector Search concepts](../concepts/vector-search.md) — how to query by meaning

