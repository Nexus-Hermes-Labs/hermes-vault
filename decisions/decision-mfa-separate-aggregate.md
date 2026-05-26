---
title: "Decision: MFA as Separate Aggregate"
tags: [decision, auth, mfa, ddd]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: MFA as Separate Aggregate

## Context

Adding MFA/TOTP support requires storing TOTP secrets, backup codes, and WebAuthn credentials. These could be added as fields on the existing [[auth-credential-entity]] or as separate domain aggregates.

## Decision

MFA/TOTP, WebAuthn, OAuth, and Password History are each **separate domain aggregates** with their own entities, repositories, and UoW writers — not extensions to AuthCredential.

## Rationale

- AuthCredential already has 19 fields; adding MFA fields would create a god object
- Each MFA method has independent lifecycle (enable/disable) and business rules
- Separate aggregates allow independent testing, migration, and feature toggling
- Follows existing Hermes pattern of atomic, focused aggregates (one concept per page/entity)

## Alternatives Considered

- **Extend AuthCredential**: Simpler initially but leads to bloated entity, complex migrations, and tight coupling between unrelated features
- **Shared MFA aggregate**: Combining TOTP + WebAuthn into one aggregate, but their flows (symmetric secret vs asymmetric key) are too different

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[totp-secret-entity]]
- [[mfa-backup-code-entity]]
- [[webauthn-credential-entity]]
- [[aggregate-root]]
