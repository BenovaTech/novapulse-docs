---
title: RAG Pipeline
description: How NovaPulse implements Retrieval-Augmented Generation to answer questions about your conversation data.
---
 
# RAG Pipeline
 
**Retrieval-Augmented Generation (RAG)** is the architecture that powers
NovaPulse's AI-driven insights. It combines the recall capability of vector
search with the reasoning capability of a large language model (LLM) to
answer natural language questions about your conversation data.
 
---
 
## Why RAG, not a standalone LLM
 
A standalone LLM cannot answer questions about *your* data:
 
- It was trained on public data up to a cutoff date
- It has no access to your call transcripts
- It will generate plausible-sounding but wrong answers (hallucinations)
 
RAG solves this by giving the LLM real, current context from your
own data before it generates a response.
 
---
 
## The three-step RAG flow
 
```mermaid
flowchart LR
  A[User query] --> B[Embed query into vector]
  B --> C[Search transcript vector store]
  C --> D[Retrieve top-k relevant segments]
  D --> E[Augment LLM prompt with context]
  E --> F[LLM generates grounded response]
  F --> G[Response with source citations]

  style A fill:#0f2027,color:#ffffff,stroke:#0d6e6e
  style B fill:#0d6e6e,color:#ffffff,stroke:#0d6e6e
  style C fill:#0d6e6e,color:#ffffff,stroke:#0d6e6e
  style D fill:#14b8a6,color:#0f2027,stroke:#14b8a6
  style E fill:#14b8a6,color:#0f2027,stroke:#14b8a6
  style F fill:#065f46,color:#ffffff,stroke:#065f46
  style G fill:#065f46,color:#ffffff,stroke:#065f46
```
 
### Step 1: Retrieve
 
The user's question is converted to a vector using NovaPulse's
embedding model. A kNN search finds the transcript segments most
semantically similar to the question.
 
### Step 2: Augment
 
The retrieved segments are injected into the LLM prompt:
 
```
Context from call transcripts:
[Segment 1: CALL-0042, timestamp 4:12] Customer: This charge is completely
unexpected. I was never told about the $49 monthly fee...
 
[Segment 2: CALL-0089, timestamp 2:31] Customer: Why is my bill so much
higher than last month? Nobody explained the price changes...
 
Based on the above context, answer: What billing concerns are customers
raising most frequently this week?
```
 
### Step 3: Generate
 
The LLM generates a response grounded in the actual transcript data,
with references to the source calls. NovaPulse includes citation links
so supervisors can navigate directly to the relevant conversation.
 
---
 
## NovaPulse's vector store
 
NovaPulse stores all transcript segment embeddings in a high-performance
vector database indexed for approximate nearest neighbor (ANN) search.
This enables sub-second semantic search across millions of transcript
segments with configurable recall-latency tradeoffs.
 
---
 
## Related
 
- [Vector Search](vector-search.md) — Embedding and kNN search explained
- [NLP Capabilities](nlp-capabilities.md) — How transcripts are generated
- [System Architecture](../architecture/system-overview.md)



