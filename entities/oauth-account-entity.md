---
title: OAuthAccount Entity
tags: [entity, domain, auth, oauth, google]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# OAuthAccount Entity

Domain entity in [[auth-service]] linking external OAuth provider identities to Hermes credentials.

## Fields

- id (UUID)
- credential_id (UUID, FK to auth_credentials)
- provider (TEXT, e.g. "google")
- provider_user_id (TEXT, unique ID from provider)
- email (TEXT, email from provider)
- created_at (TIMESTAMPTZ)
- updated_at (TIMESTAMPTZ)

## Behavior

### Find-or-Create Flow (Google callback)

1. Look up `oauth_accounts(provider, provider_user_id)` — if found, issue tokens for linked credential
2. Look up `auth_credentials` by email — if found, create link + issue tokens
3. Neither found — create new AuthCredential (random 128-bit password, email_verified=true) + OAuthAccount link + outbox event + issue tokens

### CSRF Protection

Stateless: `Base64(nonce || HMAC-SHA256(nonce, jwt_secret))`. No database storage needed. Validated on OAuth callback.

### Account Linking

Authenticated users can link/unlink Google via `POST/DELETE /api/v1/auth/oauth/google/link`. OAuth users who unlink must set a password first.

## Dependencies

- `oauth2 = "4"`, `reqwest = "0.12"`, `hmac = "0.12"`, `sha2 = "0.10"`

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[decision-oauth-csrf-stateless]]
