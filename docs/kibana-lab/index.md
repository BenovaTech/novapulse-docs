---
title: Kibana Lab
description: Hands-on Kibana exploration using NovaPulse call analytics data indexed into Elasticsearch on Elastic Cloud.
---

# Kibana Lab

This section documents hands-on exploration of Kibana using NovaPulse call
analytics data. Every page was written by actually building the things
described: indexing data, running queries, and building visualizations.
Nothing here comes from reading documentation alone.

---

## What this lab covers

| Page | What you will find |
|---|---|
| [Index NovaPulse data](kibana-index-data) | Create an Elasticsearch index mapping, index 12 sample call records using Dev Tools, and verify the data |
| [Explore with Discover](kibana-discover.md) | Use KQL to filter by sentiment, team, resolution status, and score ranges |
| [Build a Lens dashboard](kibana-lens.md) | Build a 3-panel call analytics dashboard with a sentiment pie chart, daily volume by team, and agent performance chart |

---

## Environment

- **Platform:** Elastic Cloud (free trial, GCP europe-west1)
- **Interface:** Kibana Serverless
- **Data:** NovaPulse call analytics - 12 sample records across 5 agents, 3 teams, 3 regions
- **Date range:** April 1 to April 4, 2025
- **Tools used:** Dev Tools console, Kibana Discover, Kibana Lens

---

## Why NovaPulse data

Rather than using Elastic's generic sample datasets, this lab indexes
NovaPulse-shaped data: real fields from the NovaPulse API response schema
including `overall_sentiment`, `sentiment_score`, `agent_id`, `team`,
`silence_ratio`, and `interruption_count`. This makes the visualizations
directly relevant to the platform documented in this site, and demonstrates
end-to-end thinking from API design through to data analysis.

---

!!! note "About this lab"
    These pages document real exploration, including problems encountered
    and how they were solved. The goal is to show hands-on technical
    curiosity and the ability to figure things out independently.
