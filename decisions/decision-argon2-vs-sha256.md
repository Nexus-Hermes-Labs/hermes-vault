---
title: "Decision: Argon2 for Passwords, SHA-256 for Tokens"
tags: [decision, security, hashing]
source: raw/docs/TECHNOLOGIES.md
date: 2026-05-26
status: active
---

# Decision: Argon2 for Passwords, SHA-256 for Tokens

**Status**: Accepted

- **Argon2id** for passwords: deliberately slow, memory-hard, resists GPU/ASIC attacks on human-chosen strings
- **SHA-256** for refresh/verification tokens: fast hash is sufficient because tokens have ~256 bits of entropy (rand-generated)

Different threat models → different hash strategies.

## Sources

- [[2026-05-26-technologies-doc]]

## Related

- [[auth-service]], [[jwt-manager]]
