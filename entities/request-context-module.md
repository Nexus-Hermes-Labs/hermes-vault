---
title: Request Context Module
tags: [entity, observability, tracing, infrastructure]
source: raw/codebase/request-context.rs
date: 2026-05-26
status: active
---

# Request Context Module

Per-request correlation ID and W3C trace propagation across HTTP/gRPC.

- **Location**: `services/common/src/observability/request_context.rs`

## Key Components

- `REQUEST_ID` — Tokio `task_local!` holding the current request's correlation ID
- `RequestIdScopeLayer` — Tower layer that extracts/generates request ID and sets task-local
- `RequestIdInterceptor` — Tonic interceptor injecting `x-request-id` + `traceparent` on outbound gRPC
- `HermesRequestId` — Extension type stored on the request for downstream access
- Dual `Service` impls for axum `http 1.x` and tonic `http 0.2` request types

## Sources

- [[2026-05-26-request-context]]

## Related

- [[distributed-tracing]], [[observability-stack]], [[tempo]]
