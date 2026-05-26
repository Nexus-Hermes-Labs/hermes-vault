---
title: "Source: Guild Domain Entity"
tags: [source, guild, domain, entity, ddd]
source: raw/codebase/guild-entity.rs
date: 2026-05-26
status: active
---

# Source: Guild Domain Entity

## Goal

Aggregate root for guilds (servers/communities) in guild-service.

## Key Facts Extracted

- Fields: id, owner_id, name (GuildName VO), description (max 1000 chars), icon/banner URLs, visibility (GuildVisibility VO), member_count (cached), max_members (default 500), timestamps, soft delete
- `new()` validates description length, sets owner as first member (count=1)
- Value objects: `GuildName` and `GuildVisibility` enforce invariants at construction
- Ownership model: single owner, transferable
- Member limit: 500 default, configurable per guild

## Entities Referenced

- [[guild-service]], [[guild-entity]]

## Concepts Referenced

- [[domain-driven-design]], [[aggregate-root]]

## Sources

- `raw/codebase/guild-entity.rs`

## Related

- [[2026-05-26-architecture-doc]]
- [[channel-service]]
