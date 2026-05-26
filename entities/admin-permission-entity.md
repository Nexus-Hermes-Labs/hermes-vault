---
title: AdminPermission Entity
tags: [entity, domain, admin, rbac, permission]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# AdminPermission Entity

Domain entity in [[admin-service]] representing a granular permission that can be assigned to roles.

## Fields

- id (UUID)
- name (TEXT, unique — e.g. "users.read", "users.write", "audit.read")
- description (TEXT)
- created_at (TIMESTAMPTZ)

## Permission Naming Convention

Dot-separated resource.action format: `{resource}.{action}`

Examples: `users.read`, `users.write`, `users.suspend`, `roles.manage`, `audit.read`, `permissions.manage`

## Assignment

Permissions are assigned to [[admin-role-entity]] via the `role_permissions` join table. Users inherit permissions through their assigned roles.

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[admin-service]]
- [[admin-role-entity]]
