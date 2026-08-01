# Smarter Agentic Search — Conversational Analytics for Semiconductor Fabs

> An on-premises agentic AI workflow that lets process engineers ask questions about wafer defects, yield trends, trace data and defect scan images in plain English — and get back tables, charts and images in a single chat response.

<!-- Optional badges — delete any that don't apply -->
![Status](https://img.shields.io/badge/status-prototype-blue)
![Deployment](https://img.shields.io/badge/deployment-on--premises-orange)
![Built with](https://img.shields.io/badge/built%20with-watsonx-052FAD)

📄 **This repository is documentation, not source code.** It captures the architecture, design decisions and a demo recording for the solution.

---

## Table of contents

- [The problem](#the-problem)
- [The solution](#the-solution)
- [Demo](#demo)
- [Architecture](#architecture)
- [How it works](#how-it-works)
- [Technology stack](#technology-stack)
- [Results](#results)
- [Roadmap](#roadmap)
- [Repository contents](#repository-contents)

---

## The problem

Semiconductor fab engineers lose hours of operational time manually stitching together multi-source datasets — defect logs, wafer lot metadata, equipment history, process traces, scan images — just to map basic correlations between elements. The analysis itself is the small part; the data preparation is the bottleneck.

## The solution

A reusable conversational interface, built for engineers, that turns natural-language questions into governed queries against the lakehouse and returns insight *with* the relevant visual — process charts, histograms, scatter plots, and the raw defect scan image — in one place.

Everything runs **fully on-premises**. No fab data, no IP-sensitive process information and no wafer imagery leaves the customer environment.

## Demo

<!--
  HOW TO ADD YOUR VIDEO — pick ONE of these:

  A) Native GitHub video player (best experience, 10 MB free / 100 MB paid limit):
     Edit this README in the GitHub web UI (pencil icon), put your cursor here,
     and drag the .mp4 in. GitHub uploads it and inserts a URL that renders as
     a real video player. Do NOT commit the mp4 via git and link it — that
     renders as a plain link, not a player.

  B) Clickable thumbnail linking to YouTube / Box / internal video host:
     [![Watch the demo](assets/demo/thumbnail.png)](https://your-video-url)

  C) Short GIF (works everywhere, no size trickery, ~15-30s):
     ![Demo](assets/demo/demo.gif)
-->

▶️ _Demo video goes here — see the comment in this file for the three ways to embed it._

**What the demo shows**

| # | Timestamp | Step |
|---|-----------|------|
| 1 | 0:00 | Engineer asks a defect-density question in natural language |
| 2 | 0:00 | Agent parses intent against the Business Glossary and shows its execution plan |
| 3 | 0:00 | Human-in-the-loop approval of the plan |
| 4 | 0:00 | SAL tool generates and runs Presto SQL against Iceberg tables |
| 5 | 0:00 | Table, Pareto chart and Ceph defect scan returned inline |

## Architecture

End-to-end flow: natural-language question → ReAct agent → MCP tool selection → lakehouse and object storage → table, chart and defect image returned inline. Everything inside the dashed boundary runs on-premises.

<p align="center">
  <img width="654" alt="watsonx SAL + Lakehouse architecture — semiconductor defect and trace analytics, fully on-premises" src="https://github.com/user-attachments/assets/45450367-71c4-412e-ab35-5bc48875b7ab" />
</p>

Full walkthrough: **[docs/architecture.md](docs/architecture.md)**

## How it works

1. **Data ingestion & mapping** — an on-prem orchestration engine with localized LLMs routes the natural-language query, parsing intent against a Business Glossary to standardize multi-source semiconductor data types.
2. **Automated data enrichment** — an autonomous data tool handles schema discovery and dynamic enrichment across open table formats and disparate fab datasets, cross-referencing metadata to establish precise element correlations.
3. **Explainable AI & governance** — every tool-selection step and reasoning trace is logged into a readable execution plan, gated by a human-in-the-loop validation checkpoint before anything runs.
4. **Agentic execution & querying** — on approval, specialized data tools translate the question into optimized SQL against the lakehouse tables.
5. **Visualization & storage access** — a chart rendering tool and an object storage tool produce context-specific charts and fetch raw defect scan images, returned inline in the chat.

## Technology stack

| Layer | Component |
|---|---|
| Agent runtime | watsonx Orchestrate — ReAct agent |
| Models (on-prem) | IBM Granite · gpt-oss (open-source, self-hosted) |
| Tooling protocol | MCP — SAL tool, chart rendering tool, image-from-object-storage tool |
| Query engine | Presto |
| Table format | Apache Iceberg |
| Lakehouse | watsonx.data |
| Object storage | Ceph — defect scan images (TIFF/PNG per lot & wafer) |
| Governance | Row-level access · audit trails · IP-sensitive fab data protection |
| Build accelerator | IBM Bob — architecture, SAL integration pattern, ReAct agent scaffold, diagrams |

## Results

| Metric | Before | After |
|---|---|---|
| Time per analysis use case | 5–6 hours | 2–3 hours |
| Productivity improvement in data-analysis hours | — | ~60% target |

Impact areas: reduced time and manual effort on routine tasks; improved accuracy and consistency of outputs.

## Roadmap

- [ ] Prediction agent tool for yield forecasting and predictive maintenance
- [ ] Additional fab use cases on the same reusable framework
- [ ] Expanded Business Glossary coverage across process areas

## Repository contents

```
.
├── README.md                       ← you are here
├── docs/
│   ├── architecture.md             ← component-by-component walkthrough
│   ├── solution-overview.md        ← problem, benefits, impact
│   └── demo-script.md              ← what the demo video shows, step by step
└── assets/
    ├── architecture.png         ← full flow diagram
    └── demo/                       ← video, GIF, thumbnail, screenshots
```

---

<sub>Architecture designed and diagrammed with IBM Bob.</sub>
