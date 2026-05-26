---
title: "Source: TECHNOLOGIES.md — Tech Stack Rationale"
tags: [source, tech-stack, rationale]
source: raw/docs/TECHNOLOGIES.md
date: 2026-05-26
status: active
---

# Source: TECHNOLOGIES.md — Tech Stack Rationale

## Goal

Documents every external technology with "what / why / where" structure so contributors understand why each choice was made.

## Key Facts Extracted

- Rust chosen for sub-ms latency, no GC pauses, compile-time safety
- Axum 0.7 for extractors-as-contracts HTTP framework
- Tonic 0.11 for gRPC (note: coexists with opentelemetry-otlp's internal tonic 0.12)
- SQLx 0.8 for compile-time SQL verification
- NATS over Kafka for lightweight pub/sub at current scale
- Argon2id for passwords (slow, memory-hard), SHA-256 for tokens (fast, high-entropy)
- figment + .env for configuration (Consul was rolled back)
- React 19 + Vite + TanStack Router/Query + Zustand + Tailwind + Radix/shadcn

## Decisions Found

- [[decision-consul-rolled-back]] — Consul built then removed as over-engineered
- [[decision-argon2-vs-sha256]] — different hash strategies for different threat models

## Sources

- `raw/docs/TECHNOLOGIES.md`

## Related

- [[2026-05-26-architecture-doc]]
- [[domain-driven-design]]
