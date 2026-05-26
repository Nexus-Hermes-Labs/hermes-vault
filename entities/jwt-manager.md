---
title: JwtManager
tags: [entity, security, jwt, infrastructure]
source: raw/codebase/jwt-manager.rs
date: 2026-05-26
status: active
---

# JwtManager

Shared JWT infrastructure in `common` crate. Generates and validates access/refresh tokens.

- **Location**: `services/common/src/infrastructure/security/jwt_manager.rs`
- **Algorithm**: HS256 (symmetric, separate secrets for access/refresh)
- **Claims**: RFC 7519 standard + custom (typ, email, role)

## Sources

- [[2026-05-26-jwt-manager]]

## Related

- [[auth-service]], [[gateway-authentication]]
