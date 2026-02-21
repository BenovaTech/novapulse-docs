# NovaPulse Documentation
 
[![Deploy Documentation](https://github.com/BenovaTech/novapulse-docs/actions/workflows/ci.yml/badge.svg)](https://github.com/BenovaTech/novapulse-docs/actions/workflows/ci.yml)
 
Official technical documentation for the **NovaPulse AI Voice Analytics Platform**.
Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) and
deployed to GitHub Pages via GitHub Actions.
 
**Live site:** https://BenovaTech.github.io/novapulse-docs
 
---
 
## About this project
 
NovaPulse is an AI-powered SaaS platform that transforms recorded audio
conversations into structured insights: speaker-attributed transcripts,
sentiment analysis, PII redaction, and AI-generated summaries.
 
This repository contains the complete technical documentation for the
NovaPulse API and platform, written and maintained as a
[docs-as-code](https://www.writethedocs.org/guide/docs-as-code/) project.
 
---
 
## Repository structure
 
```
novapulse-docs/
├── docs/
│   ├── index.md                  # Homepage
│   ├── concepts/                 # Conceptual guides
│   │   ├── platform-overview.md
│   │   ├── vector-search.md
│   │   ├── rag-pipeline.md
│   │   └── nlp-capabilities.md
│   ├── api-reference/            # Complete API reference
│   │   ├── authentication.md
│   │   ├── audio-ingestion.md
│   │   ├── transcription.md
│   │   └── sentiment-analysis.md
│   ├── tutorials/                # Step-by-step developer tutorials
│   ├── architecture/             # System architecture documentation
│   ├── reference/                # Glossary, error codes, rate limits
│   └── release-notes/            # Release notes and changelog
├── .github/workflows/ci.yml      # GitHub Actions deployment
├── mkdocs.yml                    # MkDocs configuration
├── requirements.txt              # Python dependencies
└── overrides/                    # MkDocs theme overrides
```
 
---
 
## Local development
 
```bash
# Clone the repository
git clone git@github.com:BenovaTech/novapulse-docs.git
cd novapulse-docs
 
# Install dependencies
pip install -r requirements.txt
 
# Run local development server
mkdocs serve
 
# Open http://127.0.0.1:8000 in your browser
```
 
---
 
## Deployment
 
The site deploys automatically via GitHub Actions on every push to `main`.
The workflow installs dependencies, builds the static site, and pushes
to the `gh-pages` branch.
 
---
 
## About
 
Written and maintained by **Houda Benlemmouden**, Technical Writer.
This is a portfolio project demonstrating technical writing methodology
for an AI SaaS platform: docs-as-code workflow, API documentation,
conceptual guides, and information architecture.
 
- LinkedIn: [linkedin.com/in/houdab86](https://linkedin.com/in/houdab86)
- Portfolio: [BenovaTech.github.io/novapulse-docs](https://BenovaTech.github.io/novapulse-docs)

