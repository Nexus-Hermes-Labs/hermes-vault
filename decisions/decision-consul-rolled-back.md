---
title: "Decision: Consul KV Rolled Back"
tags: [decision, configuration, infrastructure]
source: raw/docs/TECHNOLOGIES.md
date: 2026-05-26
status: active
---

# Decision: Consul KV Rolled Back

**Status**: Rolled back (May 2026)

Consul KV was built as a centralized config layer but rolled back as over-engineered for pre-production. Config now uses plain `.env` + figment env-var layering. Code preserved on `feat/secrets-and-config-stores` branch.

## Sources

- [[2026-05-26-technologies-doc]]

## Related

- [[auth-service]]
