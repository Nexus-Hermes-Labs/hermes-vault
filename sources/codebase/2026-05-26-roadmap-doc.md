---
title: "Source: ROADMAP.md — Development Phases and Completion"
tags: [source, roadmap, status, planning]
source: raw/docs/ROADMAP.md
date: 2026-05-26
status: active
---

# Source: ROADMAP.md — Development Phases and Completion

## Goal

Tracks the four-phase development plan and per-service completion status.

## Key Facts Extracted

- Phase 1 (MVP Core): ~85% — auth, user, guild, channel complete; chat ~70%, messaging in progress, realtime ~50%
- Phase 2 (Real-time): not started — presence, media, notification
- Phase 3 (AI): not started — text translation, STT, TTS
- Phase 4 (Scale): not started — search, voice, moderation
- Technical debt: shared DB, no Redis caching active, no gRPC integration tests

## Issues Found

- [[issue-realtime-incomplete]] — typing indicator + heartbeat blocks MVP
- [[issue-chat-endpoints-incomplete]] — remaining HTTP handlers block MVP
- [[issue-no-grpc-integration-tests]] — cross-service gRPC untested

## Sources

- `raw/docs/ROADMAP.md`

## Related

- [[2026-05-26-architecture-doc]]
- [[realtime-service]]
- [[chat-service]]
