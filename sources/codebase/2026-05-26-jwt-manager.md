---
title: "Source: JwtManager — Token Generation and Validation"
tags: [source, security, jwt, authentication]
source: raw/codebase/jwt-manager.rs
date: 2026-05-26
status: active
---

# Source: JwtManager — Token Generation and Validation

## Goal

Shared JWT infrastructure used by auth-service for token creation and the ForwardAuth verify endpoint.

## Key Facts Extracted

- Claims follow RFC 7519: iss, sub (user_id), aud, exp, nbf, iat, jti
- Custom claims: typ (access/refresh), email, role (SystemRole)
- HS256 symmetric algorithm with separate secrets for access and refresh tokens
- Access token: short-lived (6h), refresh token: long-lived (30d)
- Token type discrimination prevents using refresh token as access token
- Located in `common` crate — shared across all services that need JWT validation
- Only [[auth-service]] actually calls this; other services trust Traefik-injected headers

## Entities Referenced

- [[jwt-manager]], [[auth-service]]

## Concepts Referenced

- [[gateway-authentication]]

## Decisions Referenced

- [[decision-forward-auth]]

## Sources

- `raw/codebase/jwt-manager.rs`

## Related

- [[2026-05-26-auth-application-service]]
- [[2026-05-26-traefik-routes]]
