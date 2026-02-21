---
title: Index NovaPulse data
description: Create an Elasticsearch index mapping for NovaPulse call analytics data and index 12 sample records using Kibana Dev Tools.
---

# Index NovaPulse data

Before building visualizations, data needs to be in Elasticsearch. This page
covers creating an explicit index mapping for NovaPulse call records and
indexing 12 sample documents using the Kibana Dev Tools console.

**Official reference:** [Mapping](https://www.elastic.co/docs/manage-data/data-store/mapping)

---

## What is an index mapping?

An index mapping defines the data type of each field in an Elasticsearch index.
Getting field types right before indexing matters because Elasticsearch
cannot change a field's type after documents have been indexed.

The key decisions for NovaPulse call records:

| Field | Type | Reason |
|---|---|---|
| `@timestamp` | `date` | Enables time-based filtering and histograms in Kibana |
| `overall_sentiment` | `keyword` | Exact-match values: positive, neutral, negative |
| `sentiment_score` | `float` | Continuous value for aggregations and range queries |
| `agent_id`, `team`, `queue` | `keyword` | Used for grouping and filtering, must be exact match |
| `duration_seconds` | `integer` | Numeric field for average, min, max aggregations |
| `resolved` | `boolean` | True/false for filtering unresolved calls |

---

## Step 1: Open Dev Tools

In Kibana, click the **</>** icon at the bottom of the left sidebar to open
Dev Tools. The console has two panels: write requests on the left, view
responses on the right. Run a request with **Ctrl+Enter** or the play button.

---

## Step 2: Create the index mapping

```json
PUT novapulse-calls
{
  "mappings": {
    "properties": {
      "@timestamp":             { "type": "date" },
      "job_id":                 { "type": "keyword" },
      "agent_id":               { "type": "keyword" },
      "team":                   { "type": "keyword" },
      "call_type":              { "type": "keyword" },
      "queue":                  { "type": "keyword" },
      "duration_seconds":       { "type": "integer" },
      "overall_sentiment":      { "type": "keyword" },
      "sentiment_score":        { "type": "float" },
      "sentiment_trend":        { "type": "keyword" },
      "transcription_accuracy": { "type": "float" },
      "silence_ratio":          { "type": "float" },
      "interruption_count":     { "type": "integer" },
      "agent_talk_ratio":       { "type": "float" },
      "pipeline":               { "type": "keyword" },
      "language":               { "type": "keyword" },
      "region":                 { "type": "keyword" },
      "resolved":               { "type": "boolean" }
    }
  }
}
```

Expected response:

```json
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "novapulse-calls"
}
```

---

## Step 3: Index documents

The Elasticsearch bulk API indexes multiple documents in one request.
Each document requires two lines: an action line and a document line.

!!! warning "Bulk API formatting"
    The bulk API requires each action and each document to be on a single
    unbroken line. Kibana Dev Tools auto-formats JSON across multiple lines
    when you paste it, which causes a parse error. The workaround is to
    index documents one at a time using `POST novapulse-calls/_doc` with
    standard multi-line JSON formatting, which Dev Tools handles correctly.

Index each record individually:

```json
POST novapulse-calls/_doc
{
  "@timestamp": "2025-04-01T08:12:00Z",
  "job_id": "job_001",
  "agent_id": "AGT-12",
  "team": "billing",
  "call_type": "inbound",
  "queue": "billing",
  "duration_seconds": 312,
  "overall_sentiment": "negative",
  "sentiment_score": -0.72,
  "sentiment_trend": "declining",
  "transcription_accuracy": 0.96,
  "silence_ratio": 0.09,
  "interruption_count": 4,
  "agent_talk_ratio": 0.58,
  "pipeline": "full-analysis",
  "language": "en-US",
  "region": "emea",
  "resolved": false
}
```

Repeat for all 12 records. The full dataset is available in the
[repository](https://github.com/BenovaTech/novapulse-docs).

---

## Step 4: Verify the data

```json
GET novapulse-calls/_count
```

Expected response:

```json
{
  "count": 12
}
```

---

## Step 5: Create a data view

Kibana requires a data view to connect to an Elasticsearch index.

1. Go to **Data Management** (gear icon, bottom of left sidebar)
2. Click **Data Views**, then **Create data view**
3. Set **Name** to `NovaPulse Calls`
4. Set **Index pattern** to `novapulse-calls`
5. Set **Timestamp field** to `@timestamp`
6. Click **Save data view to Kibana**

---


## Related

- [Explore with Discover](explore-with-discover.md)
- [Build a Lens dashboard](build-lens-dashboard.md)
