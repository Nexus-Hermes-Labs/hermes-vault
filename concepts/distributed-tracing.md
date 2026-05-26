---
title: Distributed Tracing
tags: [concept, observability, tracing]
source: raw/codebase/request-context.rs
date: 2026-05-26
status: active
---

# Distributed Tracing

W3C `traceparent` header propagation across HTTP and gRPC boundaries. Spans exported to [[tempo]] via OTLP/gRPC.

## Propagation Flow

1. Inbound HTTP: `HermesMakeSpan` extracts `traceparent`, opens span with `trace_id`
2. Inbound gRPC: `RequestIdScopeService` reads from gRPC metadata
3. Outbound gRPC: `RequestIdInterceptor` injects `traceparent` + `x-request-id`
4. Export: `tracing_opentelemetry` layer forwards spans to Tempo

## Activation

Set `APP_LOGGING__OTLP_ENDPOINT=http://hermes-tempo:4317`. When unset, noop propagator.

## Sources

- [[2026-05-26-request-context]]

## Related

- [[observability-stack]], [[request-context-module]], [[tempo]]
