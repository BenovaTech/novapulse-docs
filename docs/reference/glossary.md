---
title: Glossary
description: Definitions of AI and search terms used in NovaPulse documentation.
---
 
# Glossary
 
Terms specific to NovaPulse and the AI/search concepts it uses.
 
---
 
## Dense vector
 
A fixed-length array of floating-point numbers that encodes the semantic
meaning of a piece of text. In NovaPulse, every transcript segment is
stored as a dense vector to enable semantic search. Contrast with a
*sparse vector*, which stores only non-zero values.
 
**See also:** [Vector Search](../concepts/vector-search.md)
 
---
 
## Embedding model
 
A machine learning model that converts text into a dense vector.
NovaPulse uses an embedding model to represent transcript segments
as vectors so that semantically similar segments produce numerically
similar vectors.
 
---
 
## kNN search
 
*k-nearest neighbor search.* A method for finding the k items in a
dataset whose vectors are most similar to a query vector.
NovaPulse uses kNN search to retrieve transcript segments that are
semantically closest to a natural language query.
 
**See also:** [Vector Search](../concepts/vector-search.md)
 
---
 
## Cosine similarity
 
A measure of similarity between two vectors calculated as the cosine
of the angle between them. A cosine similarity of 1 means identical
direction (same meaning). A value of 0 means orthogonal (unrelated).
Used by NovaPulse's vector search to rank results by semantic relevance.
 
---
 
## BM25
 
Best Match 25. The classic probabilistic keyword ranking algorithm
used by most search engines. BM25 scores documents based on term
frequency and inverse document frequency. Strong for exact term
matches; weak for conceptual or conversational queries.
 
---
 
## Hybrid search
 
A search strategy that combines BM25 keyword ranking with kNN
semantic search, merging results using Reciprocal Rank Fusion (RRF).
NovaPulse uses hybrid search to handle both precise term queries
and conversational natural language queries.
 
**See also:** [Vector Search](../concepts/vector-search.md)
 
---
 
## RAG (Retrieval-Augmented Generation)
 
An AI architecture that enhances LLM responses by first retrieving
relevant documents from a knowledge base and including them as context
in the prompt. RAG prevents hallucinations and enables LLMs to answer
questions about private or current data they were not trained on.
 
**See also:** [RAG Pipeline](../concepts/rag-pipeline.md)
 
---
 
## Speaker diarization
 
The process of automatically identifying and labeling different speakers
in an audio recording. NovaPulse's diarization engine assigns each
speech segment to a speaker label (Agent, Customer, or Speaker N)
without requiring prior voice enrollment.
 
---
 
## PII (Personally Identifiable Information)
 
Any data that can identify a specific individual: names, phone numbers,
email addresses, account numbers, and similar. NovaPulse's full-analysis
pipeline includes an automated PII detection and redaction module that
masks PII in transcripts before storage.
 
---
 
## Sentiment analysis
 
The automated classification of text by emotional tone. NovaPulse
produces per-segment sentiment scores (ranging from -1.0 negative to
+1.0 positive) and an overall call sentiment classification.
 
---
 
## Named Entity Recognition (NER)
 
An NLP technique for identifying and classifying named entities in text, 
people, organizations, products, locations, and dates. NovaPulse uses NER
as part of its PII detection pipeline and for extracting structured
information from unstructured conversation data.
 
---
 
## ELSER
 
*Elastic Learned Sparse Encoder.* Elastic's proprietary semantic
search model that produces sparse vector representations optimized
for English-language retrieval. Unlike dense embedding models,
ELSER does not require a separate embedding service.
 
---
 
## Inference API
 
An API that connects a search or data platform to external machine
learning models (such as OpenAI, Cohere, or Hugging Face models)
to generate embeddings, run NLP tasks, or call LLMs as part of a
search or data pipeline.


