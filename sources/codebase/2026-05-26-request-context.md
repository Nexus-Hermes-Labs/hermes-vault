---
title: "Source: Request Context — Correlation ID and Trace Propagation"
tags: [source, observability, tracing, request-id]
source: raw/codebase/request-context.rs
date: 2026-05-26
status: active
---

# Source: Request Context — Correlation ID and Trace Propagation

## Goal

Per-request correlation ID and W3C trace context propagation across HTTP and gRPC boundaries.

## Key Facts Extracted

- `REQUEST_ID` stored in Tokio `task_local!` — available to all async code in the request scope
- `RequestIdScopeLayer`: honors inbound `x-request-id` header (generates UUIDv4 if absent), stores as `HermesRequestId` extension, runs inner service in task-local scope
- `RequestIdInterceptor`: outbound gRPC interceptor that injects both `x-request-id` and W3C `traceparent` from current OpenTelemetry context
- Two separate `Service` impls: one for axum's `http 1.x` Request, one for tonic's `http 0.2` Request (different crate versions)
- `Http02HeaderExtractor` adapter bridges tonic's `MetadataMap` to OpenTelemetry's `Extractor` trait

## Concepts Referenced

- [[distributed-tracing]], [[observability-stack]]

## Entities Referenced

- [[request-context-module]]

## Sources

- `raw/codebase/request-context.rs`

## Related

- [[2026-05-26-architecture-doc]]
- [[tempo]]
