---
title: Explore with Discover
description: Use Kibana Discover and KQL to explore NovaPulse call analytics data, filter by sentiment, and analyze field distributions.
---

# Explore with Discover

Kibana Discover is the primary tool for exploring raw Elasticsearch data.
It provides a document table, field statistics panel, a time-series
histogram, and a KQL search bar. This page documents exploration of
NovaPulse call analytics data using Discover.

**Official reference:** [Discover](https://www.elastic.co/docs/explore-analyze/discover)

---

## Open Discover

1. Click **Discover** in the left sidebar
2. Select **NovaPulse Calls** from the data view selector (top left)
3. Set the time range to **April 1 to April 5, 2025** using the date picker

You should see 12 documents in the results table and a histogram showing
call distribution across the four-day period.

---

## The Discover interface

| Component | Purpose |
|---|---|
| **Search bar** | KQL query input, filters documents in real time |
| **Time range picker** | Restricts results to a specific time window |
| **Field list (left panel)** | All index fields. Click any field to see a value distribution |
| **Histogram** | Document count over time, updates instantly with every filter |
| **Document table** | Individual records. Expand any row to see all fields |

---

## KQL query examples

KQL (Kibana Query Language) is a filter language built into Kibana.
It filters which documents appear in the results but does not aggregate
or sort data. As you type, KQL suggests field names and valid values
from the index automatically.

**Official reference:** [KQL](https://www.elastic.co/docs/explore-analyze/query-filter/languages/kql)

### Filter by sentiment

```
overall_sentiment : "negative"
```

Returns 5 documents. All calls with negative sentiment classification.

### Filter by team and sentiment

```
team : "billing" AND overall_sentiment : "negative"
```

Returns billing team calls with negative sentiment only. Useful for
identifying the team with the most difficult call volume.

### Filter by numeric range

```
sentiment_score < -0.5
```

Returns calls with strongly negative sentiment scores. More precise than
the classification label and useful for escalation prioritization.

### Filter unresolved negative calls

```
resolved : false AND overall_sentiment : "negative"
```

Returns the highest-priority calls for review: unresolved and negative.
This is a query a supervisor would run every morning.

### Filter long unresolved calls

```
duration_seconds > 300 AND resolved : false
```

Returns calls over 5 minutes that were not resolved. Long duration combined
with no resolution is a strong signal of a difficult interaction.

### Wildcard filter

```
agent_id : AGT-1*
```

Returns all calls handled by agents whose ID starts with AGT-1, covering
AGT-12 and AGT-19.

---

## Analyzing field distributions

Click any field name in the left panel to see a quick value breakdown
without writing a query. This is useful for understanding data shape
before building visualizations.

**Try these:**

- Click **overall_sentiment**: shows the count split across positive,
  neutral, and negative
- Click **team**: shows call volume per team. Billing has the most calls
- Click **agent_id**: shows which agents handled the most calls
- Click **sentiment_trend**: shows the distribution of improving, stable,
  and declining trends

---

## Add columns to the document table

By default Discover shows only the timestamp and the full document source.
Add specific columns to make the table readable:

Hover over any field in the left panel and click **+** to add it as a column.
Add: **agent_id**, **team**, **overall_sentiment**, **sentiment_score**,
**duration_seconds**, **resolved**.

The table now gives a clear per-call overview, similar to a database query result.

---

## Save a search for reuse

After building a useful filter, save it to reuse in dashboards:

1. Run: `resolved : false AND overall_sentiment : "negative"`
2. Click **Save** in the application menu
3. Name it: `Unresolved Negative Calls`

Saved searches can be added directly to Kibana dashboards as panels.

---


## Related

- [Index NovaPulse data](index-novapulse-data.md)
- [Build a Lens dashboard](build-lens-dashboard.md)
- [KQL reference](https://www.elastic.co/docs/explore-analyze/query-filter/languages/kql)
