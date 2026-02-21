---
title: Changelog
description: A chronological record of all NovaPulse API and platform changes.
---

# Changelog

All notable changes to the NovaPulse API and platform are documented here.
Dates are in ISO 8601 format. Changes are grouped by release and ordered
from most recent to oldest.

For full release notes on each version, follow the links below.

---

## Versioning policy

NovaPulse follows semantic versioning:

| Change type | Example | What it means |
|---|---|---|
| **Major** | v1 to v2 | Breaking changes. A migration guide is published. Minimum 90-day deprecation notice. |
| **Minor** | v1.0 to v1.1 | New features added in a backwards-compatible way. No action required. |
| **Patch** | v1.0.0 to v1.0.1 | Backwards-compatible bug fixes and performance improvements. |

---

## Unreleased

Changes that are merged and staged but not yet in a published release.

- Real-time audio stream endpoint (Phase 2 beta)
- Support for additional languages: Spanish (`es-ES`), French (`fr-FR`)
- Speaker enrollment API for improved diarization accuracy
- Per-minute sentiment granularity via `granularity=minute` query parameter

---

## v1.0.0 — 2025-05-01

Initial General Availability release of the NovaPulse AI Voice Analytics Platform.

**New features:**

- `POST /v1/audio/ingest`: Upload audio files for processing
- `GET /v1/jobs/{job_id}`: Check processing job status
- `GET /v1/transcriptions/{job_id}`: Retrieve speaker-attributed transcripts
- `GET /v1/insights/{job_id}`: Retrieve sentiment analysis and behavioral metrics
- Webhook notifications on job completion via `webhook_url` parameter
- Automated PII detection and redaction on all transcripts
- AI-generated conversation summaries in `full-analysis` pipeline
- Export formats: JSON, SRT, VTT, TXT

**Supported audio formats:** MP3, WAV, M4A, FLAC, OGG

**Known limitations:**
- English only (`en-US`)
- Maximum file size: 500 MB
- No real-time streaming

[Full release notes](v1.0.0.md)

---

## How to stay informed

Watch the [novapulse-docs](https://github.com/BenovaTech/novapulse-docs)
repository on GitHub to receive notifications when the changelog is updated.

