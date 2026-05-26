---
title: Channel Entity — Domain Aggregate Root
tags: [source, domain, channel, aggregate-root, guild]
source: hermes-be/services/channel-service/src/domain/channel/entity.rs
date: 2026-05-26
status: active
---

# Channel Entity — Domain Aggregate Root

## Goal

Document the Channel aggregate root which models guild channels (text, voice, category, announcement).

## What Was Done

Ingested `channel-service/src/domain/channel/entity.rs` (261 lines).

## Key Facts

- **Aggregate root** for the channel-service bounded context
- Belongs to a guild (`guild_id: Uuid`), optionally nested under a parent category channel (`parent_id: Option<Uuid>`)
- Fields: id, guild_id, parent_id, name (value object `ChannelName`), type (value object `ChannelType`), description, position, timestamps, soft-delete
- **Construction**: `new()` validates description max 1024 chars; `from_persisted()` for DB rehydration
- **Update**: partial update pattern — only `Some(...)` fields are changed; self-referencing parent_id is rejected
- **Soft delete**: sets `deleted_at`, never hard-deletes
- Unit tests cover: create, update, invalid parent, soft delete

## Decisions

- [[decision-shared-database]] — channel data lives in per-service Postgres schema
- Soft-delete pattern consistent with all other aggregates

## Issues

None found — clean aggregate implementation.

## Open Threads

- No event emission on channel create/update/delete (outbox integration pending for channel-service)

## Sources

- [[2026-05-26-channel-entity]] (this page) — raw symlink `raw/codebase/channel-entity.rs`

## Related

- [[channel-service]]
- [[guild-entity]]
- [[guild-service]]
- [[aggregate-root]]
- [[domain-driven-design]]
- [[hexagonal-architecture]]
