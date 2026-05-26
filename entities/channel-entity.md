---
title: Channel Entity (Aggregate Root)
tags: [entity, domain, channel, aggregate-root]
source: hermes-be/services/channel-service/src/domain/channel/entity.rs
date: 2026-05-26
status: active
---

# Channel Entity (Aggregate Root)

Domain aggregate root for the [[channel-service]]. Represents a text, voice, category, or announcement channel within a guild.

## Structure

| Field | Type | Notes |
|---|---|---|
| id | `Uuid` | Primary key |
| guild_id | `Uuid` | Owning guild |
| parent_id | `Option<Uuid>` | Parent category channel |
| name | `ChannelName` | Validated value object |
| type | `ChannelType` | text, voice, category, announcement |
| description | `Option<String>` | Max 1024 chars |
| position | `i32` | Display order |
| created_at / updated_at | `DateTime<Utc>` | Timestamps |
| deleted_at | `Option<DateTime<Utc>>` | Soft delete |

## Business Rules

- Self-referencing parent_id is rejected (`InvalidParentId`)
- Description limited to 1024 characters
- Partial update: only `Some(...)` fields are modified
- Soft delete only (sets `deleted_at`, never hard-deletes)

## Sources

- [[2026-05-26-channel-entity]]

## Related

- [[channel-service]]
- [[guild-entity]]
- [[guild-service]]
- [[aggregate-root]]
- [[domain-driven-design]]
