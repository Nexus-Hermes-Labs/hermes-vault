---
title: Graceful Shutdown
tags: [concept, operations, lifecycle]
source: raw/docs/PATTERNS.md
date: 2026-05-26
status: active
---

# Graceful Shutdown

A `tokio::sync::watch` channel broadcasts the shutdown signal. HTTP server, gRPC server, and all background tasks hold a receiver and exit cleanly via `tokio::select!`.

## Sources

- [[2026-05-26-patterns-doc]]

## Related

- [[domain-driven-design]]
