---
title: "Issue: Realtime Service — Typing and Heartbeat Missing"
tags: [issue, realtime, mvp-blocker]
source: raw/docs/ROADMAP.md
date: 2026-05-26
status: active
---

# Issue: Realtime Service — Typing and Heartbeat Missing

**Severity**: High — blocks MVP completion

[[realtime-service]] is ~50% complete. Missing features:
- Typing indicator fanout to channel subscribers
- Heartbeat / keepalive mechanism
- Complete NATS → WebSocket bridge for all event types
- Reconnection handling

## Sources

- [[2026-05-26-roadmap-doc]]

## Related

- [[realtime-service]], [[chat-service]]
