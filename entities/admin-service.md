---
title: admin-service
tags: [entity, service, admin, rbac]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# admin-service

Backend service for system-level user management and RBAC (Role-Based Access Control).

- **HTTP port**: 8095
- **gRPC port**: 50058
- **Status**: Planned
- **Codebase**: `hermes-be/services/admin-service/`
- **Database**: `hermes_admin` (shared Postgres instance)

## Purpose

Provides administrative operations: user management (suspend, activate, unlock, delete), system-level role/permission management, and audit log viewing. Delegates credential mutations to [[auth-service]] via gRPC.

## Key Characteristics

- 6-layer DDD scaffold following [[guild-service]] pattern
- System-level roles (user, moderator, admin) — distinct from guild-service's per-guild bitflag roles
- Uses ForwardAuth like all services; `AdminOnly` extractor checks `X-User-Role == "admin"`
- gRPC client → [[auth-service]] for credential operations (suspend, activate, unlock, force-password-reset, disable-mfa, list/get/delete users)
- gRPC client → [[auth-service]] for `GetAuditLogs` (audit dashboard)

## Domain Entities

- [[admin-role-entity]] — `id, name, description, is_system, created_at, updated_at`
- [[admin-permission-entity]] — `id, name (e.g. "users.read"), description, created_at`
- UserRole — `user_id, role_id, assigned_at, assigned_by`
- RolePermission — `role_id, permission_id, assigned_at`

## Endpoints

All require admin role via `AdminOnly` extractor.

### User Management (gRPC → auth-service)
| Method | Path | Purpose |
|---|---|---|
| GET | `/api/v1/admin/users` | List users (paginated) |
| GET | `/api/v1/admin/users/:id` | Get user details |
| POST | `/api/v1/admin/users/:id/suspend` | Suspend user |
| POST | `/api/v1/admin/users/:id/activate` | Activate user |
| POST | `/api/v1/admin/users/:id/unlock` | Unlock locked account |
| DELETE | `/api/v1/admin/users/:id` | Delete user |
| POST | `/api/v1/admin/users/:id/force-password-reset` | Force password reset |
| DELETE | `/api/v1/admin/users/:id/mfa` | Disable user's MFA |

### Role + Permission Management
| Method | Path | Purpose |
|---|---|---|
| CRUD | `/api/v1/admin/roles` | Role management |
| CRUD | `/api/v1/admin/permissions` | Permission management |
| POST/DELETE | `/api/v1/admin/roles/:id/permissions/:pid` | Assign/revoke permission |
| POST/DELETE | `/api/v1/admin/users/:id/roles/:rid` | Assign/revoke user role |

### Audit
| Method | Path | Purpose |
|---|---|---|
| GET | `/api/v1/admin/audit-logs` | Paginated, filterable audit log |

## Seed Data

System roles seeded in migrations (immutable): `user` (default), `moderator`, `admin`.

## Workspace Integration

- Cargo workspace member: `services/admin-service`
- Makefile: `DOCKER_SERVICES`, `DB_SERVICES`, `RUN_DB_SERVICES`
- Traefik route: `PathPrefix("/api/v1/admin")` → `hermes-admin-service:8095`, jwt-auth middleware
- Config: `config/services/admin-service.toml`

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-service]], [[guild-service]]
- [[admin-role-entity]], [[admin-permission-entity]]
- [[decision-admin-service-separation]]
- [[gateway-authentication]]
