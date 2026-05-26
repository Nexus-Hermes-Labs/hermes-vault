---
title: "Issue: Chat/Messaging Service Port Discrepancy"
tags: [issue, configuration, documentation]
source: raw/codebase/traefik-routes.yml
date: 2026-05-26
status: active
---

# Issue: Chat/Messaging Service Port Discrepancy

**Severity**: Medium — documentation inconsistency

Traefik `routes.yml` shows:
- chat-service on port **8094**
- messaging-service on port **8084**

But ARCHITECTURE.md and ROADMAP.md list chat-service on **8084**. One of these is wrong — either the docs or the Traefik config.

## Root Cause

Likely the services were re-assigned ports when messaging-service was added, but docs were not updated to reflect the new chat-service port.

## Sources

- [[2026-05-26-traefik-routes]]

## Related

- [[chat-service]], [[messaging-service]], [[traefik]]
