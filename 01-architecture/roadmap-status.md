# Roadmap Status

**Last verified against codebase: May 2026**

## Phase Overview

| Phase | Focus | Status |
|---|---|---|
| Phase 1 | MVP Core (auth, users, guilds, channels, chat, realtime) | ~85% |
| Phase 2 | Real-time Supporting (presence, media, notifications) | Not started |
| Phase 3 | AI Innovation (text translation, STT, TTS) | Not started |
| Phase 4 | Scale and Polish (search, voice, moderation) | Not started |

## Service Completion Matrix

| Service | Domain | Application | Infrastructure | Presentation | Tests | Overall |
|---|---|---|---|---|---|---|
| Traefik | N/A | N/A | Done | N/A | N/A | **Complete** |
| auth-service | Done | Done | Done | Done | Done | **Complete** |
| user-service | Done | Done | Done | Done | Done | **Complete** |
| guild-service | Done | Done | Done | Done | Done | **Complete** |
| channel-service | Done | Done | Done | Done | Done | **Complete** |
| chat-service | Done | Done | In progress | In progress | Partial | **~70%** |
| messaging-service | Done | Done | In progress | In progress | Partial | **In progress** |
| realtime-service | Done | In progress | In progress | In progress | Partial | **~50%** |

## Phase 1 Remaining Work

- **chat-service**: Remaining HTTP endpoints/DTOs, full NATS event publishing, test coverage
- **realtime-service**: Typing indicator fanout, heartbeat/keepalive, complete NATS→WS bridge
- **Integration tests**: gRPC cross-service flows not yet tested end-to-end

## Observability Completion

Fully shipped as of May 2026:
- Structured JSON logs + Promtail → Loki
- Prometheus metrics (request counters + latency histograms)
- Grafana dashboards (hermes-overview)
- Distributed tracing via OpenTelemetry → Tempo
- Cross-linking: Loki ↔ Tempo (trace_id)

## Technical Debt

| Item | Severity | Notes |
|---|---|---|
| Shared PostgreSQL (all services, one instance) | Low | Acceptable for MVP; plan per-service DB post-MVP |
| Redis caching not yet activated | Low | Infrastructure wired, logic not applied |
| No gRPC integration tests | Medium | Cross-service calls untested end-to-end |
| ROADMAP.md in hermes-be says "Last Updated: March 25, 2026" | Low | Vault may be more current than docs |

## Known Discrepancies (Vault ↔ Codebase Docs)

1. **TECHNOLOGIES.md** still documents Consul KV + figment — Consul was rolled back (May 2026). Config uses plain `.env` now.
2. **ARCHITECTURE.md** service table shows channel-service as "Stubbed" and gRPC port as "—" — but channel-service is fully implemented with gRPC port 50053.
3. **ARCHITECTURE.md** shows user-service as "~60%" and guild-service as "~40%" — both are now complete.
4. **messaging-service** exists in Cargo workspace but is not listed in ARCHITECTURE.md or ROADMAP.md service tables.
