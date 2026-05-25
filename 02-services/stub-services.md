# Stub Services

These services exist in the Cargo workspace but have no implementation yet. They use `traefik/whoami` as placeholder containers.

## presence-service (Phase 2, port 8087)

Online/offline/idle/DND status tracking, typing indicators, last seen timestamps.

Planned approach:
- Redis-backed ephemeral presence state
- NATS subscription for connect/disconnect events from realtime-service
- gRPC interface for other services to query presence

## media-service (Phase 2, port 8088)

File uploads, image processing (thumbnails, format conversion), avatar management.

Planned approach:
- Multipart upload via Axum
- Storage abstraction: local filesystem (dev), S3-compatible (prod)
- Image processing pipeline

## notification-service (Phase 2, port 8089)

Push notifications, unread message counts, @mention tracking.

Planned approach:
- NATS consumer for message and mention events
- Per-user notification state in PostgreSQL
- Future: FCM/APNs integration

## search-service (Phase 4, port 8090)

Full-text search across messages, users, guilds.

Planned approach:
- PostgreSQL tsvector/tsquery for MVP
- Optional Elasticsearch/Meilisearch for scale
- Permission-aware results

## ai-service (Phase 3, port 8091)

The differentiating feature — real-time AI-powered translation.

Sub-phases:
- **3a**: Text translation (language detection + translation via external AI API)
- **3b**: Voice STT (speech-to-text with live translated subtitles)
- **3c**: Voice TTS (full speech-to-speech pipeline, <500ms target)

## voice-service (Phase 4, port 8085)

WebRTC P2P signaling, voice channel management.

Planned approach:
- Signaling server for WebRTC peer setup
- STUN/TURN coordination
- Integration with ai-service for voice translation
