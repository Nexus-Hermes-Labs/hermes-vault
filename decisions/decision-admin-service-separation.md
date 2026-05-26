---
title: "Decision: Separate Admin Service for RBAC"
tags: [decision, admin, rbac, architecture]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: Separate Admin Service for RBAC

## Context

System-level user management (suspend, activate, delete) and RBAC (roles, permissions) need a home. Options: extend auth-service, extend user-service, or create a new admin-service.

## Decision

Create a **new admin-service** (port 8095/50058) that owns system-level RBAC and delegates credential mutations to auth-service via gRPC.

## Rationale

- Auth-service stays authentication-only (single responsibility)
- Admin operations are a separate bounded context: role management, permission assignment, user lifecycle administration
- gRPC delegation keeps credential mutations in auth-service's domain (where AuthCredential lives)
- New service follows established Hermes pattern: one bounded context per microservice
- Port 8087 is reserved for presence-service (Phase 2 roadmap), so admin-service uses 8095/50058

## System Roles vs Guild Roles

| System Roles (admin-service) | Guild Roles (guild-service) |
|---|---|
| Platform-wide access control | Per-guild permissions |
| user, moderator, admin | Custom roles with i64 bitflags |
| Governs admin operations | Governs channel/member permissions |
| Stored in `hermes_admin` DB | Stored in `hermes_guild` DB |

## Alternatives Considered

- **Extend auth-service**: Violates SRP, bloats a complete service
- **Extend user-service**: Conflates user profile management with admin operations
- **Monolithic admin panel**: Not microservice-aligned

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[admin-service]]
- [[auth-service]]
- [[guild-service]]
- [[admin-role-entity]]
- [[admin-permission-entity]]
