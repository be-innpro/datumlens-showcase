# Datumlens

> **Sovereign, GDPR-by-design data governance & conversational BI platform — deployed in 2–4 hours.**
> A be-innpro product. Submitted to the **START Rise Challenge 2026**.

[![Status](https://img.shields.io/badge/status-MVP%20%C2%B7%20TRL%206--7-blue)]()
[![License](https://img.shields.io/badge/license-Proprietary%20%C2%B7%20INPI%20filed-orange)]()
[![Stack](https://img.shields.io/badge/stack-Python%20%E2%80%A2%20Node.js%20%E2%80%A2%20Kubernetes-success)]()

---

## What it is

Datumlens is a self-hosted, multi-tenant data hub that combines four capabilities most enterprises buy from four different vendors:

1. **Discovery & cataloguing** of heterogeneous databases (PostgreSQL, MongoDB, Oracle, MS-SQL, BigQuery, S3, Kafka)
2. **GDPR/LGPD classification** with 8-category PII detection and contextual masking
3. **Conversational BI** — natural-language SQL generation in Portuguese
4. **Auto-generated dashboards** through an LLM-driven Apache Superset spec generator

Everything runs inside the customer's tenant. The data **never leaves customer infrastructure**.

## Problem

European SMEs and mid-market enterprises face a structural data-governance gap:

- **752,000 Portuguese SMEs** lack the internal capacity to adopt AI (only 8.6% have).
- A full data team costs **>€180,000/year** — out of reach for 90% of the market.
- Enterprise platforms (Palantir, Informatica, Collibra, Snowflake) start at **€200K** and require **6–12-month implementations**.
- **GDPR + AI Act** compliance is mandatory by 2025–2026, with average fines of €85K.

Datumlens collapses cost and time-to-value by **10×** — deploys in **2–4 hours** at SME-affordable pricing.

## Architecture

```
                        ┌─────────────────────────────────────────────┐
                        │            Datumlens Tenant (K8s)            │
                        │                                             │
   Customer Data ───►   │  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
   (PG, Mongo, BQ,      │  │Discovery │  │Quality   │  │Governance│  │
    Oracle, S3, ...)    │  │Agent     │  │Agent     │  │Agent     │  │
                        │  └──────────┘  └──────────┘  └──────────┘  │
                        │  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
                        │  │Queries   │  │Insights  │  │Operations│  │
                        │  │Agent     │  │Agent     │  │Agent     │  │
                        │  └──────────┘  └──────────┘  └──────────┘  │
                        │  ┌──────────┐  ┌──────────┐  ┌──────────┐  │
                        │  │Financial │  │Commercial│  │Orchestr. │  │
                        │  │Agent     │  │Agent     │  │(LangGraph│  │
                        │  └──────────┘  └──────────┘  └──────────┘  │
                        │                                             │
                        │  Apache Atlas · Apache Superset · pgvector  │
                        │  Keycloak SSO · ELK · YData Profiling       │
                        │                                             │
                        │  5-tier LLM routing:                        │
                        │  ┌────────────────────────────────────────┐ │
                        │  │ Sonnet 4.6 → Haiku 4.5 → Qwen3 30B →   │ │
                        │  │   OpenRouter → local Ollama (fallback) │ │
                        │  └────────────────────────────────────────┘ │
                        └─────────────────────────────────────────────┘
```

## 9 specialised agents

| Agent | Role | Differentiator |
|---|---|---|
| **Queries** | NL → SQL in natural Portuguese | Hybrid RAG (pgvector + FTS + RRF + reranker) |
| **Discovery** | Auto-catalogue heterogeneous sources | End-to-end lineage; 8 source adapters |
| **Quality** | DQ continuous, anomaly detection | Auto-correction loops |
| **Governance** | LGPD/GDPR mapping, retention, audit | **Active** governance vs. passive (industry-first) |
| **Financial** | P&L, margins, forecasts | Live BI views, no refresh |
| **Commercial** | Revenue analysis, cohort retention | Explanations bound to evidence |
| **Insights** | Anomaly + opportunity detection | Causal probing |
| **Operations** | Health, throughput, SLO tracking | Auto-paging via Discord/Slack/Teams |
| **Orchestrator** | Routes work, enforces policies | LangGraph StateGraph + circuit breaker |

## Tech stack

| Layer | Technology |
|---|---|
| **Backend** | Python 3.12 · FastAPI |
| **Frontend** | Node.js 20 · Express |
| **Identity** | Keycloak (OIDC SSO) |
| **Metadata** | Apache Atlas (lineage) |
| **BI** | Apache Superset (embedded with guest tokens) |
| **Storage** | PostgreSQL 16 + pgvector · MongoDB 7 |
| **Observability** | ELK stack · X-Trace-ID end-to-end |
| **Profiling** | YData Profiling |
| **Infra** | Docker Compose (single-node) → Kubernetes (multi-tenant) |
| **Reverse proxy** | Nginx with TLS termination |

## Computational footprint

- **Today (single-tenant Docker Compose)**: 8–16 vCPU · 32–64 GB RAM · ~500 GB storage. CPU-bound; no GPU required.
- **At 10× scale (multi-tenant K8s)**: ~241 concurrent tenants × 4–8 GPU-hours/week = ~1,000–2,000 GPU-hours/week steady-state. Liquid-cooled, low-PUE GPU substrate becomes essential.
- **Inference**: stateless API calls (Anthropic, OpenRouter) with circuit-breaker fallback to local Ollama. Customer always controls *which* LLM runs.

## Why "managed VPS / dedicated server" delivery model

- **Data sovereignty**: customer data never leaves the tenant. We operate the tenant; we do not see the data.
- **Compliance**: each tenant is its own GDPR/LGPD scope; audit trail is local; revocation is instant.
- **Performance**: liquid-cooled, GPU-resident inference at ~0.5 ms latency from Lisbon dramatically improves NL→SQL response time vs. cloud-API round-trips.
- **Cost**: a single SINES-class campus replaces multiple cloud egress contracts; Microsoft Azure interconnect collapses egress on hybrid workloads.

## Stage & evidence

- **TRL 6/7** · v0.2 in production internally · live beta pilot
- **9 specialised agents shipped**
- **2–4-hour deployment validated** vs. 6–12-month industry baseline
- **Roadmap** to v0.5 documented (6 ADRs approved)

## Intellectual property

- **INPI Utility Model** `20262007740964` (Portugal — under analysis)
- **INPI Trademark Class 42** `20262007739058` (Portugal — under analysis)
- **IGAC copyright registration** on FORJA Process methodology

## Pricing (illustrative, B2B)

- **Starter (€2K–€4K/month)**: small SMEs, single-tenant on managed VPS
- **Professional (€4K–€8K/month)**: mid-market, multi-source discovery + governance
- **Enterprise (€8K–€10K/month)**: regulated sectors, dedicated cluster, custom SLAs

Average target ARR per customer: **€131,700/year** (mix 10/60/30 startup/SME/enterprise).

## Roadmap (high level)

| Version | Milestone |
|---|---|
| v0.2 | (Current) 9 agents, NL→SQL, Superset embed |
| v0.3 | Unified chat agent, hybrid RAG, dynamic model router, glossary, Kibana dashboards |
| v0.4 | Multi-tenant Kubernetes GA, public-sector compliance pack |
| v0.5 | EU expansion connectors, sector-specific accelerators (financial, healthcare) |

## About be-innpro

be-innpro builds **sovereign agentic AI for Europe** — a unified stack of three production products (Datumlens, ORMAI, Nakori) on one orchestration core. Founded in Lisbon by **José Maria Duque (CEO)** and **Neilsen Alves Seixas (CTO)**.

Website: [https://be-innpro.com](https://be-innpro.com)

## Status of this repository

This repository hosts a **public showcase only** — architecture, screenshots, README. The Datumlens source code is **not public** because the corresponding INPI Utility Model is currently under analysis. The full product is available as a managed deployment; contact us for access.

## Contact

- **CEO & Founder** — José Maria Duque — `jose@be-innpro.com`
- **CTO & Co-Founder** — Neilsen Alves Seixas — `neilsen@be-innpro.com`

---

© 2026 be-innpro. All rights reserved. Datumlens, ORMAI, and FORJA Process are protected by pending INPI Utility Model and trademark filings.
