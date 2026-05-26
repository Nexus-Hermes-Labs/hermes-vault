---
title: Observability Stack
tags: [concept, observability, monitoring]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Observability Stack

Three pillars fully shipped: logs, metrics, traces. Unified in [[grafana]].

| Signal | Stack | Endpoint |
|---|---|---|
| Logs | tracing → Promtail → [[loki]] | :3100 |
| Metrics | metrics crate → [[prometheus]] | :9090 |
| Traces | OpenTelemetry → [[tempo]] | :4317 |
| Dashboards | [[grafana]] | :3000 |

## Cross-Linking

- Loki → Tempo: `trace_id` in JSON logs becomes clickable link to trace
- Tempo → Loki: `tracesToLogsV2` jumps from span to related logs

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-infra-docker-compose]]

## Related

- [[distributed-tracing]], [[request-context-module]]
