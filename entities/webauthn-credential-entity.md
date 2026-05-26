---
title: WebAuthnCredential Entity
tags: [entity, domain, auth, webauthn, passkey]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# WebAuthnCredential Entity

Domain entity in [[auth-service]] for FIDO2/WebAuthn passkey credentials.

## Fields

- id (UUID)
- credential_id_bytes (TEXT, base64-encoded credential ID from authenticator)
- user_id (UUID)
- public_key_json (JSONB, serialized public key)
- name (TEXT, user-given label like "YubiKey 5")
- sign_count (INTEGER, replay protection counter)
- created_at (TIMESTAMPTZ)
- last_used_at (TIMESTAMPTZ)

## Behavior

- Registration: two-step (begin → complete). Challenge stored in [[redis]] with key `webauthn:{user_id}:reg`, TTL 300s
- Authentication: two-step (begin → complete). Challenge stored in [[redis]] with key `webauthn:{user_id}:auth`, TTL 300s
- Sign count incremented on each authentication to detect cloned authenticators
- Repository trait: `find_by_user(user_id)`, `find_by_credential_id(cred_id)`, `save(...)`, `update_counter(id, count)`, `delete(id)`

## Dependencies

- `webauthn-rs = "0.5"` with `danger-allow-state-serialisation` feature for Redis challenge storage

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[redis]]
