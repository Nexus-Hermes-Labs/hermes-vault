---
title: "Decision: Config-Based Password Policy"
tags: [decision, auth, password, configuration]
source: 2026-05-26-auth-enhancement-plan
date: 2026-05-26
status: active
---

# Decision: Config-Based Password Policy

## Context

Password validation rules (min length, complexity) need to be enforced on registration and password reset. These rules could be stored in the database or loaded from configuration.

## Decision

Password policy is a **config-based value object** loaded from `auth-service.toml` under `[password_policy]`. Not stored in the database.

## Configuration

```toml
[password_policy]
min_length = 8
max_length = 128
require_uppercase = true
require_digit = true
require_special = false
history_size = 5
```

## Rationale

- Hermes is single-tenant — no need for per-tenant policy storage
- Config changes require service restart, which is acceptable for password policy (rarely changes)
- Simpler implementation: no migration, no repository, no admin UI for policy management
- Follows Hermes convention of Figment-based config loading from .env + env vars

## Alternatives Considered

- **DB-stored policy**: Needed for multi-tenant SaaS where each tenant has different rules. Overkill here.
- **Hardcoded constants**: Too rigid, no way to adjust without code change.

## Sources

- [[2026-05-26-auth-enhancement-plan]]

## Related

- [[auth-credential-entity]]
- [[password-history-entity]]
