---
title: "Source: Infrastructure Docker Compose"
tags: [source, infrastructure, docker, containers]
source: raw/codebase/infra-docker-compose.yml
date: 2026-05-26
status: active
---

# Source: Infrastructure Docker Compose

## Goal

Defines all infrastructure containers: databases, caches, message brokers, and observability stack.

## Key Facts Extracted

- PostgreSQL 16 (hermes-postgres:5432) with init.sql for per-service databases
- Redis 7 (hermes-redis:6379) for sessions and caching
- NATS 2.10 (hermes-nats:4222/8222) with JetStream enabled
- Prometheus (hermes-prometheus:9090) scraping all services every 15s
- Loki (hermes-loki:3100) with 7-day retention, TSDB storage
- Promtail (hermes-promtail) auto-discovers hermes-* containers via Docker socket
- Tempo (hermes-tempo:3200/4317) for distributed trace storage
- Grafana (hermes-grafana:3000) with auto-provisioned datasources (Prometheus, Loki, Tempo)
- Mailpit (hermes-mailpit:1025/8025) for dev email capture
- All on `hermes-network` (external)

## Entities Referenced

- [[postgresql]], [[redis]], [[nats-jetstream]], [[prometheus]], [[loki]], [[tempo]], [[grafana]]

## Sources

- `raw/codebase/infra-docker-compose.yml`

## Related

- [[2026-05-26-architecture-doc]]
- [[observability-stack]]
