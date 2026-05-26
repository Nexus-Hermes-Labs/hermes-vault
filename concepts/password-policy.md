---
title: Password Policy
tags: [concept, auth, security, validation]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: draft
---

# Password Policy

Config-based value object in [[auth-service]] that validates password strength on registration and password reset.

## Rules

| Rule | Default | Purpose |
|---|---|---|
| min_length | 8 | Minimum password length |
| max_length | 128 | Maximum password length |
| require_uppercase | true | At least one uppercase letter |
| require_digit | true | At least one digit |
| require_special | false | At least one special character |
| history_size | 5 | Number of previous passwords to check against |

## Implementation

- Loaded from `auth-service.toml` `[password_policy]` section via Figment
- Returns `Vec<PolicyViolation>` on failure (multiple rules can fail simultaneously)
- History check uses [[password-history-entity]] — Argon2 verify against recent N hashes

## Sources

- [[2026-05-26-auth-enhancement-plan]]
- [[decision-password-policy-config]]

## Related

- [[auth-credential-entity]]
- [[password-history-entity]]
- [[decision-argon2-vs-sha256]]
