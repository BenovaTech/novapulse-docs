---
title: NovaPulse Documentation
description: >-
  Complete developer documentation for the NovaPulse AI Voice
  Analytics Platform — API reference, tutorials, and architecture.
---
 
# NovaPulse Documentation
 
**NovaPulse** is an AI-powered Voice Analytics Platform that transforms
raw audio conversations into structured, actionable intelligence.
Built for enterprise teams in customer support, sales, HR, and healthcare,
NovaPulse delivers transcription, sentiment analysis, speaker diarization,
and autonomous voice agent capabilities through a single REST API.
 
---
 
## Quick navigation
 
=== "New to NovaPulse"
    **Start here if you're integrating NovaPulse for the first time.**
 
    1. Read the [Platform Overview](concepts/platform-overview.md)
       to understand what NovaPulse does and how it's structured.
    2. Follow the [Getting Started tutorial](tutorials/getting-started.md)
       to make your first API call in under 10 minutes.
    3. Explore the [API Reference](api-reference/index.md) for complete
       endpoint documentation.
 
=== "Building with the API"
    **For developers actively integrating NovaPulse.**
 
    - [Authentication](api-reference/authentication.md) — API keys and headers
    - [Audio Ingestion API](api-reference/audio-ingestion.md) — Upload audio files
    - [Transcription API](api-reference/transcription.md) — Retrieve transcripts
    - [Sentiment Analysis API](api-reference/sentiment-analysis.md) — Query insights
 
=== "Understanding the platform"
    **Conceptual guides for deeper understanding.**
 
    - [Vector Search in NovaPulse](concepts/vector-search.md)
    - [RAG Pipeline architecture](concepts/rag-pipeline.md)
    - [NLP capabilities](concepts/nlp-capabilities.md)
    - [System architecture](architecture/system-overview.md)
 
---
 
## Platform capabilities
 
| Capability | Description | Phase |
|---|---|---|
| **Transcription** | Speaker-attributed text from audio with ≥90% accuracy | GA |
| **Sentiment analysis** | Per-segment sentiment scoring and trend detection | GA |
| **Speaker diarization** | Automatic speaker identification and labeling | GA |
| **PII redaction** | Automated detection and redaction of personal data | GA |
| **Real-time analytics** | Live audio stream processing with ≤2s latency | Beta |
| **Autonomous agents** | Context-aware AI voice agents for full conversations | Planned |
 
---
 
## Documentation status
 
| Section | Status | Last updated |
|---|---|---|
| API Reference | :white_check_mark: Complete | 2025-05-01 |
| Tutorials | :white_check_mark: Complete | 2025-05-01 |
| Concepts | :white_check_mark: Complete | 2025-05-01 |
| Architecture | :white_check_mark: Complete | 2025-05-01 |
| Reference | :white_check_mark: Complete | 2025-05-01 |
| Release Notes | :white_check_mark: Complete | 2025-05-01 |
 
---
 
!!! note "About this documentation"
    This documentation site is a portfolio project demonstrating
    technical writing methodology for an AI SaaS platform.
    Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/),
    deployed via GitHub Actions, and maintained using a docs-as-code workflow.
    — [Houda Benlemmouden](https://linkedin.com/in/houdab86)

