---
title: "Decision: Traefik as Edge Proxy"
tags: [decision, architecture, traefik]
source: raw/docs/ARCHITECTURE.md
date: 2026-05-26
status: active
---

# Decision: Traefik as Edge Proxy

**Status**: Accepted (early 2026)

Replaced a custom Rust API gateway with Traefik v3. ForwardAuth centralizes JWT validation in [[auth-service]], Docker provider auto-discovers containers, file provider allows hot-reload of routes. Battle-tested vs. building our own.

## Sources

- [[2026-05-26-architecture-doc]]
- [[2026-05-26-technologies-doc]]

## Related

- [[traefik]], [[decision-forward-auth]], [[gateway-authentication]]
