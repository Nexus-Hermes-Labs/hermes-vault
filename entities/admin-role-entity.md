---
title: AdminRole Entity
tags: [entity, domain, admin, rbac, role]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# AdminRole Entity

Domain entity in [[admin-service]] representing a system-level role for RBAC.

## Fields

- id (UUID)
- name (TEXT, unique — e.g. "admin", "moderator", "user")
- description (TEXT)
- is_system (BOOLEAN — true for seeded roles, prevents deletion)
- created_at (TIMESTAMPTZ)
- updated_at (TIMESTAMPTZ)

## System Roles (seeded, immutable)

| Name | Description |
|---|---|
| user | Default role assigned to all registered users |
| moderator | Can moderate content and users |
| admin | Full system access |

## Distinction from Guild Roles

These are **system-level** roles. [[guild-service]] has its own per-guild bitflag role system (`guild_roles` table with i64 permissions) that governs channel/member permissions within a guild. System roles govern platform-wide access.

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[admin-service]]
- [[admin-permission-entity]]
- [[guild-service]]
- [[decision-admin-service-separation]]
