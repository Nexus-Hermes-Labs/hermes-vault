---
title: Security and Authentication
tags: [synthesis, security, authentication, jwt, traefik]
source: multiple
date: 2026-05-26
status: active
---

# Security and Authentication

Cross-cutting synthesis of Hermes' security architecture: how users authenticate, how tokens flow, and how services verify identity.

## Authentication Flow

1. **Login**: Client sends credentials to [[auth-service]] via HTTP
2. **Credential check**: [[auth-credential-entity]] validates against Argon2-hashed password (see [[decision-argon2-vs-sha256]])
3. **Token issuance**: [[jwt-manager]] generates access + refresh token pair
4. **Subsequent requests**: Client includes JWT in `Authorization` header

## ForwardAuth Pattern

All API traffic passes through [[traefik]] reverse proxy. Traefik's ForwardAuth middleware sends every request to [[auth-service]]'s verify endpoint before forwarding to the target service.

- **Only auth-service parses JWTs** — other services never touch tokens
- Auth-service extracts user identity and injects `X-User-Id` header
- Target services read identity from trusted headers via [[request-context-module]]
- See [[decision-forward-auth]] for rationale

## Password Security

- **Argon2id** with tuned parameters (see [[decision-argon2-vs-sha256]])
- No plaintext passwords stored; credential entity only holds hash
- Password change events published via [[transactional-outbox]]

## Token Management

- [[jwt-manager]] in common crate handles token creation and validation
- Access tokens are short-lived; refresh tokens allow session renewal
- Session tracking via auth_session entity in [[auth-service]]

## Open Issues

- [[issue-realtime-incomplete]] — WebSocket auth flow not yet implemented
- Auth integration test gaps (see memory: `project_auth_test_gaps.md`)

## Sources

- [[2026-05-26-auth-application-service]]
- [[2026-05-26-auth-credential-entity]]
- [[2026-05-26-jwt-manager]]
- [[2026-05-26-request-context]]

## Related

- [[service-architecture-overview]]
- [[gateway-authentication]]
- [[auth-service]]
- [[traefik]]
