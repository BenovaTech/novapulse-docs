---
title: Vector Search in NovaPulse
description: How NovaPulse uses vector embeddings to enable semantic search across voice conversation data.
---
 
# Vector Search in NovaPulse
 
Traditional keyword search looks for exact word matches. When a supervisor
searches call transcripts for *"cancellation"*, they will miss transcripts
where a customer said *"I want to leave"* or *"thinking about switching"*.
For voice analytics, this is a critical limitation.
 
NovaPulse uses **vector search** — also called semantic search — to find
content based on meaning rather than exact words.
 
---
 
## How vector embeddings work
 
When NovaPulse processes a transcript, each segment of speech is passed
through an embedding model that converts the text into a **dense vector**:
a list of numbers that encodes the semantic meaning of the text.
 
```
"I want to cancel my subscription"  →  [0.23, -0.87, 0.44, 0.91, ...]
"thinking about switching providers" →  [0.21, -0.89, 0.46, 0.88, ...]  ← similar
"great experience today, thank you"  →  [-0.91, 0.33, -0.67, 0.12, ...] ← different
```
 
Texts with similar meaning produce vectors that are mathematically close
together. Distance between vectors is measured using **cosine similarity**.
 
---
 
## Semantic search in NovaPulse
 
When you use the Insights API to search conversations, NovaPulse:
 
1. Converts your query into a vector using the same embedding model
2. Performs a **k-nearest neighbor (kNN) search** across all stored transcript vectors
3. Returns the conversations whose vectors are closest to your query vector
4. Ranks results by semantic similarity, not keyword frequency
 
### Example
 
A support manager queries:
 
```
"calls where customers expressed frustration about billing"
```
 
NovaPulse returns relevant calls even if they contain none of those
exact words — including transcripts where customers said *"this charge
makes no sense"*, *"I'm really annoyed about this invoice"*, or
*"nobody explained the fees to me"*.
 
---
 
## Hybrid search
 
For queries involving exact terms — product names, error codes,
or precise phrases — pure semantic search can underperform.
NovaPulse supports **hybrid search** that combines:
 
| Method | Strengths |
|---|---|
| **BM25 (keyword)** | Exact term matching, product names, error codes |
| **kNN (semantic)** | Concept matching, synonyms, conversational language |
| **Hybrid (RRF)** | Best results for most real-world queries |
 
Results from both methods are merged using **Reciprocal Rank Fusion (RRF)**,
which combines rankings without requiring score normalization.
 
---
 
## Related concepts
 
- [RAG Pipeline](rag-pipeline.md) — How NovaPulse uses retrieval for AI answers
- [NLP Capabilities](nlp-capabilities.md) — Transcription, diarization, sentiment
- [Glossary](../reference/glossary.md) — Definitions of all AI terms



