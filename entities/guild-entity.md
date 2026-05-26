---
title: Guild Entity
tags: [entity, domain, guild, aggregate-root]
source: raw/codebase/guild-entity.rs
date: 2026-05-26
status: active
---

# Guild Entity

Aggregate root for guilds in [[guild-service]]. Represents a server/community.

## Fields

- id, owner_id, name (GuildName VO), description (max 1000), icon/banner URLs
- visibility (GuildVisibility VO), member_count (cached), max_members (default 500)
- Timestamps, soft delete

## Sources

- [[2026-05-26-guild-entity]]

## Related

- [[guild-service]], [[channel-service]], [[aggregate-root]]
